---
title: 'Code First-Migrationen: EF6'
description: Code-First-Migrationen in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/migrations/index
ms.openlocfilehash: aec3a53816e41bf7ead696fd9edc30618d58b07a
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072160"
---
# <a name="code-first-migrations"></a>Code First-Migrationen
Code First-Migrationen ist die empfohlene Methode, das Datenbankschema einer Anwendung weiterzuentwickeln, wenn Sie den Code First-Workflow verwenden. Code First-Migrationen stellt eine Reihe von Tools bereit, mit denen Sie folgende Aktionen ausführen können:

1. Erstellen einer Ausgangsdatenbank, die mit Ihrem EF-Modell funktioniert
2. Generieren von Migrationen, um Änderungen an Ihrem EF-Modell nachzuverfolgen
2. Aktualisieren der Datenbank mit den entsprechenden Änderungen

Diese exemplarische Vorgehensweise bietet eine Übersicht über Code First-Migrationen in Entity Framework. Sie können entweder die gesamte exemplarische Vorgehensweise ausführen oder mit einem Thema fortfahren, das Sie besonders interessiert. Die folgenden Themen werden erörtert:

## <a name="building-an-initial-model--database"></a>Erstellen eines Ausgangsmodells und einer Ausgangsdatenbank

Bevor Sie Migrationen verwenden können, benötigen Sie ein Projekt und ein Code First-Modell. In dieser exemplarischen Vorgehensweise werden die kanonischen Modelle **Blog** und **Post** verwendet.

-   Erstellen Sie eine neue **MigrationsDemo**-Konsolenanwendung.
-   Fügen Sie dem Projekt die neueste Version des NuGet-Pakets **EntityFramework** hinzu.
    -   Wählen Sie **Tools > Bibliotheks-Paket-Manger > Paket-Manager-Konsole** aus.
    -   Führen Sie den Befehl **Install-Package EntityFramework** aus.
-   Fügen Sie eine **Model.cs**-Datei mit dem unten aufgeführten Code hinzu. Mit diesem Code wird eine einzelne **Blog**-Klasse, die das Domänenmodell bildet, und eine **BlogContext**-Klasse, die den EF Code First-Kontext darstellt, definiert.

  ``` csharp
      using System.Data.Entity;
      using System.Collections.Generic;
      using System.ComponentModel.DataAnnotations;
      using System.Data.Entity.Infrastructure;

      namespace MigrationsDemo
      {
          public class BlogContext : DbContext
          {
              public DbSet<Blog> Blogs { get; set; }
          }

          public class Blog
          {
              public int BlogId { get; set; }
              public string Name { get; set; }
          }
      }
  ```

-   Mit dem Modell können Sie nun auf die Daten zugreifen. Aktualisieren Sie die **Program.cs**-Datei mit dem folgenden Code:

  ``` csharp
      using System;
      using System.Collections.Generic;
      using System.Linq;
      using System.Text;

      namespace MigrationsDemo
      {
          class Program
          {
              static void Main(string[] args)
              {
                  using (var db = new BlogContext())
                  {
                      db.Blogs.Add(new Blog { Name = "Another Blog " });
                      db.SaveChanges();

                      foreach (var blog in db.Blogs)
                      {
                          Console.WriteLine(blog.Name);
                      }
                  }

                  Console.WriteLine("Press any key to exit...");
                  Console.ReadKey();
              }
          }
      }
  ```

-   Wenn Sie nun die Anwendung ausführen, wird die Datenbank **MigrationsCodeDemo.BlogContext** erstellt.

    ![Datenbank LocalDB](~/ef6/media/databaselocaldb.png)

## <a name="enabling-migrations"></a>Aktivieren von Migrationen

Im Folgenden erfahren Sie, wie Sie weitere Änderungen am Modell vornehmen.

-   Fügen Sie der Blog-Klasse eine URL-Eigenschaft hinzu.

``` csharp
    public string Url { get; set; }
```

Wenn Sie zu diesem Zeitpunkt die Anwendung erneut ausführen würden, würden Sie die Ausnahme „InvalidOperationException“ mit folgendem Text erhalten: *The model backing the 'BlogContext' context has changed since the database was created. Consider using Code First Migrations to update the database (* [ *http://go.microsoft.com/fwlink/?LinkId=238269* ](https://go.microsoft.com/fwlink/?LinkId=238269) *) (Das Modell, das den Kontext „BlogContext“ unterstützt, wurde seit der Erstellung der Datenbank verändert. Verwenden Sie Code First-Migrationen, um die Datenbank zu aktualisieren.).*

Beginnen Sie daher mit der Verwendung von Code First-Migrationen. Aktivieren Sie dafür zunächst Migrationen für den Kontext.

-   Führen Sie in der Paket-Manager-Konsole den Befehl **Enable-Migrations** aus.

    Mit diesem Befehl wird dem Projekt ein **Migrations**-Ordner hinzugefügt. Der neue Ordner enthält zwei Dateien:

-   **Die Dateikonfigurationsklasse:** Mit dieser Klasse können Sie das Verhalten von Migrationen für den Kontext konfigurieren. Verwenden Sie für diese exemplarische Vorgehensweise die Standardkonfiguration.
    *Da in Ihrem Projekt nur ein einzelner Code First-Kontext vorhanden ist, wurde der Kontexttyp, der für diese Konfiguration gilt, automatisch von „Enable-Migrations“ ausgefüllt.*
-   **Eine InitialCreate-Migration:** Diese Migration wurde generiert, da Code First bereits vor dem Aktivieren von Migrationen eine Datenbank erstellt hat. Der Code in diesem Entwurf für die Migration stellt die Objekte dar, die in der Datenbank bereits erstellt wurden. In unserem Fall handelt es sich dabei um die **Blog**-Tabelle mit einer **BlogId** und **Namen**-Spalten. Zur besseren Sortierung enthält der Dateiname einen Zeitstempel.
    *Wäre die Datenbank nicht bereits erstellt worden, wäre die InitialCreate-Migration dem Projekt nicht hinzugefügt worden. Stattdessen wäre beim ersten Aufruf von „Add-Migration“ der Code zum Erstellen dieser Tabellen als Entwurf für eine neue Migration verwendet worden.*

### <a name="multiple-models-targeting-the-same-database"></a>Mehrere Modelle mit derselben Zieldatenbank

In Versionen vor EF6 konnte nur ein Code First-Modell zum Generieren/Verwalten eines Datenbankschemas verwendet werden. Dies ist das Ergebnis einer einzelnen **\_\_MigrationsHistory**-Tabelle pro Datenbank, ohne Möglichkeit, die Zuordnung eines Eintrags zu einem Modell zu identifizieren.

Ab EF6 enthält die **Configuration**-Klasse eine **ContextKey**-Eigenschaft. Diese dient als eindeutiger Bezeichner für jedes Code First-Modell. Durch eine entsprechende Spalte in der **\_\_MigrationsHistory**-Tabelle können Einträge aus mehreren Modellen die Tabelle gemeinsam nutzen. Die Standardeinstellung für diese Eigenschaft ist der vollqualifizierte Name des Kontexts.

## <a name="generating--running-migrations"></a>Generieren und Ausführen von Migrationen

Code First-Migrationen verfügt über zwei primäre Befehle, die Sie im Folgenden kennenlernen werden:

-   **Add-Migration** erstellt den Entwurf für die nächste Migration basierend auf Änderungen, die Sie seit dem Erstellen der letzten Migration an Ihrem Modell vorgenommen haben.
-   **Update-Database** übernimmt alle ausstehenden Migrationen für die Datenbank.

Erstellen Sie einen Entwurf für die Migration, um die neu hinzugefügte URL-Eigenschaft zu übernehmen. Mit dem **Add-Migration**-Befehl können Sie diesen Migrationen einen Namen geben, z.B. **AddBlogUrl**.

-   Führen Sie in der Paket-Manager-Konsole den Befehl **Add-Migration AddBlogUrl** aus.
-   Im **Migrations**-Ordner befindet sich nun eine neue **AddBlogUrl**-Migration. Zur besseren Sortierung wurde der Dateiname für die Migration vorab mit einem Zeitstempel versehen.

``` csharp
    namespace MigrationsDemo.Migrations
    {
        using System;
        using System.Data.Entity.Migrations;

        public partial class AddBlogUrl : DbMigration
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

Zu diesem Zeitpunkt kann die Migration bei Bedarf bearbeitet werden, oder ihr kann etwas hinzugefügt werden. Übernehmen Sie die Migration mit **Update-Database** für die Datenbank.

-   Führen Sie in der Paket-Manager-Konsole den Befehl **Update-Database** aus.
-   Mit Code First-Migrationen werden die Migrationen im **Migrations**-Ordner mit den für die Datenbank übernommenen Migrationen verglichen. Dabei wird festgestellt, dass die Migration **AddBlogUrl** übernommen und ausgeführt werden muss.

Die aktualisierte Datenbank **MigrationsDemo.BlogContext** enthält nun in der **Blogs**-Tabelle die Spalte **URL**.

## <a name="customizing-migrations"></a>Anpassen von Migrationen

Bisher haben Sie eine Migration generiert und ausgeführt, ohne Änderungen vorzunehmen. Nun erfahren Sie, wie Sie standardmäßig generierten Code bearbeiten können.

-   Fügen Sie zunächst der **Blog**-Klasse eine neue **Rating**-Eigenschaft hinzu.

``` csharp
    public int Rating { get; set; }
```

-   Fügen Sie zudem eine neue **Post**-Klasse hinzu.

``` csharp
    public class Post
    {
        public int PostId { get; set; }
        [MaxLength(200)]
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public Blog Blog { get; set; }
    }
```

-   Fügen Sie anschließend der **Blog**-Klasse eine **Posts**-Sammlung hinzu, die das andere Ende der Beziehung zwischen **Blog** und **Post** bilden soll.

``` csharp
    public virtual List<Post> Posts { get; set; }
```

Verwenden Sie den Befehl **Add-Migration**, damit Code First-Migrationen die am besten passenden Migrationen für Sie entwirft. Geben Sie dieser Migration den Namen **AddPostClass**.

-   Führen Sie in der Paket-Manager-Konsole den Befehl **Add-Migration AddPostClass** aus.

Der Entwurf für die Änderungen wurde von Code First-Migrationen bestmöglich erstellt. Nun sollten Sie einige Änderungen vornehmen:

1.  Fügen Sie zunächst der Spalte **Posts.Title** einen eindeutigen Index hinzu (indem Sie untenstehendem Code die Zeilen 22 und 29 hinzufügen).
2.  Fügen Sie außerdem eine **Blogs.Rating**-Spalte hinzu, die keine NULL-Werte zulässt. Sind in der Tabelle Daten vorhanden, wird ihnen der CLR-Standarddatentyp für die neue Spalte zugewiesen („Rating“ erfordert einen ganzzahligen Wert, in diesem Fall **0**). Damit die vorhandenen Spalten der **Blogs**-Tabelle mit einem vernünftigen Rating-Wert beginnen, geben Sie den Standardwert **3** an.
    (Der angegebene Standardwert wird im folgenden Code in Zeile 24 angezeigt.)

``` csharp
    namespace MigrationsDemo.Migrations
    {
        using System;
        using System.Data.Entity.Migrations;

        public partial class AddPostClass : DbMigration
        {
            public override void Up()
            {
                CreateTable(
                    "dbo.Posts",
                    c => new
                        {
                            PostId = c.Int(nullable: false, identity: true),
                            Title = c.String(maxLength: 200),
                            Content = c.String(),
                            BlogId = c.Int(nullable: false),
                        })
                    .PrimaryKey(t => t.PostId)
                    .ForeignKey("dbo.Blogs", t => t.BlogId, cascadeDelete: true)
                    .Index(t => t.BlogId)
                    .Index(p => p.Title, unique: true);

                AddColumn("dbo.Blogs", "Rating", c => c.Int(nullable: false, defaultValue: 3));
            }

            public override void Down()
            {
                DropIndex("dbo.Posts", new[] { "Title" });
                DropIndex("dbo.Posts", new[] { "BlogId" });
                DropForeignKey("dbo.Posts", "BlogId", "dbo.Blogs");
                DropColumn("dbo.Blogs", "Rating");
                DropTable("dbo.Posts");
            }
        }
    }
```

Die bearbeitete Migration ist jetzt einsatzbereit. Aktualisieren Sie daher die Datenbank mit **Update-Database**. Geben Sie dieses Mal das Flag **–Verbose** an, damit das SQL angezeigt wird, mit dem Code First-Migrationen ausgeführt wird.

-   Führen Sie in der Paket-Manager-Konsole den Befehl **Update-Database –Verbose** aus.

## <a name="data-motion--custom-sql"></a>Verschieben von Daten mit benutzerdefiniertem SQL

Bisher haben Sie nur Migrationsvorgänge kennengelernt, bei denen die Daten weder geändert noch verschoben werden. Im Folgenden werden auch Vorgänge mit Datenverschiebungen behandelt. Für Datenverschiebungen ist derzeit noch keine native Unterstützung verfügbar. Doch Sie können jederzeit einige beliebige SQL-Befehle im Skript ausführen.

-   Fügen Sie dem Modell eine **Post.Abstract**-Eigenschaft hinzu. Später erfahren Sie, wie Sie für vorhandene Beiträge die **Abstract**-Spalte mit Text vom Anfang der **Content**-Spalte vorab auffüllen.

``` csharp
    public string Abstract { get; set; }
```

Verwenden Sie den Befehl **Add-Migration**, damit Code First-Migrationen die am besten passenden Migrationen für Sie entwirft.

-   Führen Sie in der Paket-Manager-Konsole den Befehl **Add-Migration AddPostAbstract** aus.
-   Die generierte Migration übernimmt die Änderungen des Schemas. Doch es soll auch die **Abstract**-Spalte mit den ersten 100 Zeichen des Inhalts für jeden Beitrag vorab aufgefüllt werden. Führen Sie hierzu in SQL die **UPDATE**-Anweisung aus, nachdem die Spalte hinzugefügt wurde
    (indem Sie untenstehendem Code die Zeile 12 hinzufügen).

``` csharp
    namespace MigrationsDemo.Migrations
    {
        using System;
        using System.Data.Entity.Migrations;

        public partial class AddPostAbstract : DbMigration
        {
            public override void Up()
            {
                AddColumn("dbo.Posts", "Abstract", c => c.String());

                Sql("UPDATE dbo.Posts SET Abstract = LEFT(Content, 100) WHERE Abstract IS NULL");
            }

            public override void Down()
            {
                DropColumn("dbo.Posts", "Abstract");
            }
        }
    }
```

Die Migration ist nun fertig bearbeitet. Aktualisieren Sie daher die Datenbank mit **Update-Database**. Geben Sie das Flag **–Verbose** an, damit das SQL angezeigt wird, das auf der Datenbank ausgeführt wird.

-   Führen Sie in der Paket-Manager-Konsole den Befehl **Update-Database –Verbose** aus.

## <a name="migrate-to-a-specific-version-including-downgrade"></a>Migrieren zu einer bestimmten Version (einschließlich Downgrade)

Bisher haben Sie immer ein Upgrade auf die neueste Migration durchgeführt, doch manchmal möchten Sie vielleicht ein Upgrade/Downgrade auf eine bestimmte Migration durchführen.

Angenommen, Sie möchten die Datenbank zu dem Zustand nach der **AddBlogUrl**-Migration migrieren. Verwenden Sie den Schalter **–TargetMigration**, um ein Downgrade auf diese Migration durchzuführen.

-   Führen Sie in der Paket-Manager-Konsole den Befehl **Update-Database –TargetMigration: AddBlogUrl** aus.

Mit diesem Befehl wird das Down-Skript für die Migrationen **AddBlogAbstract** und **AddPostClass** ausgeführt.

Verwenden Sie zum Zurücksetzen auf eine leere Datenbank den Befehl **Update-Database –TargetMigration: $InitialDatabase**.

## <a name="getting-a-sql-script"></a>Abrufen eines SQL-Skripts

Andere Entwickler können diese Änderungen auf ihren Computer übertragen, indem sie eine Synchronisierung durchführen, nachdem Sie die Änderungen in die Quellcodeverwaltung eingecheckt haben. Sobald die neuen Migrationen bei ihnen angekommen sind, müssen sie nur noch den Befehl „Update-Database“ ausführen, um die Änderungen lokal zu übernehmen. Wenn Sie jedoch diese Änderungen auf einen Testserver und eventuell zur Produktion weiterleiten möchten, benötigen Sie vermutlich ein SQL-Skript, das Sie an den DBA übergeben können.

-   Führen Sie den Befehl **Update-Database** aus, doch geben Sie dieses Mal das Flag **–Script** an, damit die Änderungen nicht übernommen, sondern in ein Skript geschrieben werden. Geben Sie zudem eine Quell- und Zielmigration an, für die das Skript generiert werden soll. Das Skript sollte von einer leeren Datenbank (**$InitialDatabase**) bis zur neuesten Version (**AddPostAbstract**-Migration) reichen.
    *Wenn Sie keine Zielmigration angeben, verwendet Code First-Migrationen die neueste Migration als Ziel. Wenn Sie keine Quellmigrationen angeben, verwendet Code First-Migrationen den aktuellen Zustand der Datenbank.*
-   Führen Sie in der Paket-Manager-Konsole den Befehl **Update-Database -Script -SourceMigration: $InitialDatabase -TargetMigration: AddPostAbstract** aus.

Code First-Migrationen führt die Migrationspipeline aus. Doch anstatt die Änderungen tatsächlich zu übernehmen, werden sie in eine SQL-Datei geschrieben. Nachdem das Skript generiert wurde, wird es in Visual Studio geöffnet. Dort können Sie es sich ansehen oder speichern.

### <a name="generating-idempotent-scripts"></a>Generieren von idempotenten Skripts

Ab EF6 wird mit der Angabe von **–SourceMigration $InitialDatabase** ein idempotentes Skript generiert. Mit idempotenten Skripts kann für eine Datenbank ein Upgrade von einer beliebigen Version auf die neueste Version (oder die angegebene Version, wenn Sie **–TargetMigration** verwenden) durchgeführt werden. Das generierte Skript enthält Logik zum Überprüfen der **\_\_MigrationsHistory**-Tabelle. Es werden nur Änderungen übernommen, die nicht schon zuvor übernommen wurden.

## <a name="automatically-upgrading-on-application-startup-migratedatabasetolatestversion-initializer"></a>Automatisches Upgrade beim Starten der Anwendung (Initialisierer „MigrateDatabaseToLatestVersion“)

Beim Bereitstellen der Anwendung sollten Sie festlegen, dass für die Datenbank beim Starten der Anwendung automatisch ein Upgrade durchgeführt wird (indem die ausstehenden Migrationen übernommen werden). Registrieren Sie hierzu den Datenbankinitialisierer **MigrateDatabaseToLatestVersion**. Ein Datenbankinitialisierer enthält Logik, mit der sichergestellt wird, dass die Datenbank ordnungsgemäß eingerichtet ist. Diese Logik wird bei der ersten Verwendung des Kontexts im Anwendungsprozess ausgeführt (**AppDomain**).

Sie können die Datei **Program.cs** wie unten dargestellt so aktualisieren, dass der Initialisierer **MigrateDatabaseToLatestVersion** auf „BlogContext“ festgelegt wird, bevor der Kontext verwendet wird (Zeile 14). Beachten Sie, dass Sie für den Namespace **System.Data.Entity** auch eine Using-Anweisung hinzufügen müssen (Zeile 5).

*Beim Erstellen einer Instanz von diesem Initialisierer müssen Sie den Kontexttyp (**BlogContext**) und die Migrationskonfiguration (**Configuration**) angeben. Die Migrationskonfiguration ist die Klasse, die dem **Migrations**-Ordner beim Aktivieren von Migrationen hinzugefügt wurde.*

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Data.Entity;
    using MigrationsDemo.Migrations;

    namespace MigrationsDemo
    {
        class Program
        {
            static void Main(string[] args)
            {
                Database.SetInitializer(new MigrateDatabaseToLatestVersion<BlogContext, Configuration>());

                using (var db = new BlogContext())
                {
                    db.Blogs.Add(new Blog { Name = "Another Blog " });
                    db.SaveChanges();

                    foreach (var blog in db.Blogs)
                    {
                        Console.WriteLine(blog.Name);
                    }
                }

                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();
            }
        }
    }
```

Die Anwendung überprüft nun bei jeder Ausführung zunächst, ob die Zieldatenbank auf dem neuesten Stand ist, und übernimmt gegebenenfalls ausstehende Migrationen.
