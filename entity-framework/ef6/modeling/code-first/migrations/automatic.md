---
title: Automatisches Code First-Migrationen-EF6
description: Automatisches Code First-Migrationen in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/migrations/automatic
ms.openlocfilehash: e7bd9ff7d9dcecb7fecf213306047a53fc04135c
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072563"
---
# <a name="automatic-code-first-migrations"></a>Automatisches Code First-Migrationen
Automatische Migrationen ermöglichen es Ihnen, Code First-Migrationen zu verwenden, ohne dass eine Codedatei in Ihrem Projekt für jede von Ihnen vornimmt. Nicht alle Änderungen können automatisch angewendet werden, z. b. für Spalten Umbenennungen ist die Verwendung einer Code basierten Migration erforderlich.

> [!NOTE]
> In diesem Artikel wird davon ausgegangen, dass Sie wissen, wie Code First-Migrationen in einfachen Szenarien verwendet wird. Andernfalls müssen Sie [Code First-Migrationen](xref:ef6/modeling/code-first/migrations/index) lesen, bevor Sie fortfahren.

## <a name="recommendation-for-team-environments"></a>Empfehlung für Team Umgebungen

Sie können automatische und Code basierte Migrationen interagieren, dies wird jedoch in Team Entwicklungsszenarien nicht empfohlen. Wenn Sie Teil eines Teams von Entwicklern sind, das die Quell Code Verwaltung verwendet, sollten Sie entweder reine automatische Migrationen oder rein Code basierte Migrationen verwenden. Aufgrund der Einschränkungen für automatische Migrationen empfiehlt sich die Verwendung von Code basierten Migrationen in Team Umgebungen.

## <a name="building-an-initial-model--database"></a>Erstellen eines Ausgangsmodells und einer Ausgangsdatenbank

Bevor Sie Migrationen verwenden können, benötigen Sie ein Projekt und ein Code First-Modell. In dieser exemplarischen Vorgehensweise werden die kanonischen Modelle **Blog** und **Post** verwendet.

-   Erstellen einer neuen **migrationsautomaticdemo** -Konsolenanwendung
-   Fügen Sie dem Projekt die neueste Version des NuGet-Pakets **EntityFramework** hinzu.
    -   Wählen Sie **Tools > Bibliotheks-Paket-Manger > Paket-Manager-Konsole** aus.
    -   Führen Sie den Befehl **Install-Package EntityFramework** aus.
-   Fügen Sie eine **Model.cs**-Datei mit dem unten aufgeführten Code hinzu. Mit diesem Code wird eine einzelne **Blog**-Klasse, die das Domänenmodell bildet, und eine **BlogContext**-Klasse, die den EF Code First-Kontext darstellt, definiert.

  ``` csharp
      using System.Data.Entity;
      using System.Collections.Generic;
      using System.ComponentModel.DataAnnotations;
      using System.Data.Entity.Infrastructure;

      namespace MigrationsAutomaticDemo
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

      namespace MigrationsAutomaticDemo
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

-   Führen Sie die Anwendung aus, und Sie werden feststellen, dass eine **migrationsautomaticcodedemo. blogcontext** -Datenbank für Sie erstellt wird.

    ![Localdb-Datenbank](~/ef6/media/databaselocaldb.png)

## <a name="enabling-migrations"></a>Aktivieren von Migrationen

Im Folgenden erfahren Sie, wie Sie weitere Änderungen am Modell vornehmen.

-   Fügen Sie der Blog-Klasse eine URL-Eigenschaft hinzu.

``` csharp
    public string Url { get; set; }
```

Wenn Sie zu diesem Zeitpunkt die Anwendung erneut ausführen würden, würden Sie die Ausnahme „InvalidOperationException“ mit folgendem Text erhalten: *The model backing the 'BlogContext' context has changed since the database was created. Consider using Code First Migrations to update the database (* [ *http://go.microsoft.com/fwlink/?LinkId=238269* ](https://go.microsoft.com/fwlink/?LinkId=238269) *) (Das Modell, das den Kontext „BlogContext“ unterstützt, wurde seit der Erstellung der Datenbank verändert. Verwenden Sie Code First-Migrationen, um die Datenbank zu aktualisieren.).*

Beginnen Sie daher mit der Verwendung von Code First-Migrationen. Da wir automatische Migrationen verwenden möchten, geben wir den Schalter **– enableautomaticmigrationen** an.

-   Führen Sie den Befehl **enable-Migrationen – enableautomaticmigrationen** in der Paket-Manager-Konsole aus. dieser Befehl hat dem Projekt einen **Migrations** Ordner hinzugefügt. Dieser neue Ordner enthält eine Datei:

-   **Die Dateikonfigurationsklasse:** Mit dieser Klasse können Sie das Verhalten von Migrationen für den Kontext konfigurieren. Verwenden Sie für diese exemplarische Vorgehensweise die Standardkonfiguration.
    *Da in Ihrem Projekt nur ein einzelner Code First-Kontext vorhanden ist, wurde der Kontexttyp, der für diese Konfiguration gilt, automatisch von „Enable-Migrations“ ausgefüllt.*

 

## <a name="your-first-automatic-migration"></a>Ihre erste automatische Migration

Code First-Migrationen verfügt über zwei primäre Befehle, die Sie im Folgenden kennenlernen werden:

-   **Add-Migration** erstellt den Entwurf für die nächste Migration basierend auf Änderungen, die Sie seit dem Erstellen der letzten Migration an Ihrem Modell vorgenommen haben.
-   **Update-Database** übernimmt alle ausstehenden Migrationen für die Datenbank.

Wir werden die Verwendung der Add-Migration vermeiden (es sei denn, wir brauchen dies wirklich) und konzentrieren uns darauf, dass Code First-Migrationen die Änderungen automatisch berechnen und anwenden. Verwenden Sie " **Update-Database** ", um Code First-Migrationen, die Änderungen an unser Modell (die neue Eigenschaft " **Blog. ur**l") an die Datenbank zu überführen.

-   Führen Sie den Befehl **Update-Database** in der Paket-Manager-Konsole aus.

Die **migrationsautomaticdemo. blogcontext** -Datenbank wurde nun aktualisiert und enthält nun die **URL** -Spalte in der **Blogs** -Tabelle.

 

## <a name="your-second-automatic-migration"></a>Ihre zweite automatische Migration

Nehmen wir eine weitere Änderung vor, und lassen Sie Code First-Migrationen die Änderungen für uns automatisch per Push an die Datenbank überführen.

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

Verwenden Sie jetzt **Update-Database** , um die Datenbank auf den neuesten Stand zu bringen. Geben Sie dieses Mal das Flag **–Verbose** an, damit das SQL angezeigt wird, mit dem Code First-Migrationen ausgeführt wird.

-   Führen Sie in der Paket-Manager-Konsole den Befehl **Update-Database –Verbose** aus.

## <a name="adding-a-code-based-migration"></a>Hinzufügen einer Code basierten Migration

Sehen wir uns nun an, wie wir eine Code basierte Migration für verwenden möchten.

-   Fügen Sie der **Blog** -Klasse eine **Bewertungs** Eigenschaft hinzu.

``` csharp
    public int Rating { get; set; }
```

Wir könnten einfach " **Update-Database** " ausführen, um diese Änderungen per Push in die Datenbank zu überführen. Allerdings fügen wir eine **Blogs. Rating** -Spalte hinzu, die keine NULL-Werte zulässt. Wenn in der Tabelle vorhandene Daten vorhanden sind, wird ihr die CLR-Standardeinstellung des Datentyps für die neue Spalte zugewiesen (die Bewertung ist Integer, d. h. **0**). Damit die vorhandenen Spalten der **Blogs**-Tabelle mit einem vernünftigen Rating-Wert beginnen, geben Sie den Standardwert **3** an.
Verwenden Sie den Befehl "Add-Migration", um diese Änderung in eine Code basierte Migration zu schreiben, damit wir Sie bearbeiten können. Der Befehl " **Add-Migration** " ermöglicht es uns, diesen Migrationen einen Namen zu geben. wir nennen einfach "unsere **addblograting**".

-   Führen Sie den Befehl **Add-Migration addblograting** in der Paket-Manager-Konsole aus.
-   Im **Migrations** Ordner verfügen wir nun über eine neue Migration von **addblograting** . Der Dateiname der Migration ist mit einem Zeitstempel versehen, um die Reihenfolge zu unterstützen. Wir bearbeiten den generierten Code, um den Standardwert 3 für Blog. Rating (Zeile 10 im folgenden Code) anzugeben.

*Die Migration umfasst auch eine Code Behind-Datei, in der einige Metadaten erfasst werden. Diese Metadaten ermöglichen es Code First-Migrationen, die automatischen Migrationen zu replizieren, die wir vor dieser Code basierten Migration durchgeführt haben. Dies ist wichtig, wenn ein anderer Entwickler unsere Migrationen ausführen möchte oder wenn es an der Zeit ist, die Anwendung bereitzustellen.*

``` csharp
    namespace MigrationsAutomaticDemo.Migrations
    {
        using System;
        using System.Data.Entity.Migrations;

        public partial class AddBlogRating : DbMigration
        {
            public override void Up()
            {
                AddColumn("Blogs", "Rating", c => c.Int(nullable: false, defaultValue: 3));
            }

            public override void Down()
            {
                DropColumn("Blogs", "Rating");
            }
        }
    }
```

Die Migration ist nun fertig bearbeitet. Aktualisieren Sie daher die Datenbank mit **Update-Database**.

-   Führen Sie den Befehl **Update-Database** in der Paket-Manager-Konsole aus.

## <a name="back-to-automatic-migrations"></a>Zurück zu automatischen Migrationen

Wir können nun für unsere einfacheren Änderungen wieder zu automatischen Migrationen wechseln. Code First-Migrationen übernimmt die automatische und Code basierte Migration in der richtigen Reihenfolge basierend auf den Metadaten, die Sie in der Code-Behind-Datei für jede Code basierte Migration speichert.

-   Fügen wir dem Modell eine Post. Abstract-Eigenschaft hinzu.

``` csharp
    public string Abstract { get; set; }
```

Nun können wir mit **Update-Database** Code First-Migrationen, diese Änderung mithilfe einer automatischen Migration an die Datenbank zu übernehmen.

-   Führen Sie den Befehl **Update-Database** in der Paket-Manager-Konsole aus.

## <a name="summary"></a>Zusammenfassung

In dieser exemplarischen Vorgehensweise wurde erläutert, wie automatische Migrationen verwendet werden, um Modelländerungen per Push an die Datenbank zu überführen Sie haben auch gesehen, wie Sie Code basierte Migrationen zwischen automatischen Migrationen aufbauen und ausführen können, wenn Sie mehr Kontrolle benötigen.
