---
title: Exemplarische Vorgehensweise für Entitäten mit selbst Nachverfolgung EF6
description: Exemplarische Vorgehensweise für Entitäten mit selbst Nachverfolgung für Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/disconnected-entities/self-tracking-entities/walkthrough
ms.openlocfilehash: 398be11d330f5a7413f5a84424217ea3eda446ef
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072797"
---
# <a name="self-tracking-entities-walkthrough"></a>Exemplarische Vorgehensweise zur Self-Tracking
> [!IMPORTANT]
> Es wird nicht mehr empfohlen, die Vorlage für Entitäten mit Selbstnachverfolgung zu verwenden. Die Vorlage ist nur für die Unterstützung vorhandener Anwendungen weiterhin verfügbar. Wenn für Ihre Anwendung die Arbeit mit getrennten Diagrammen von Entitäten erforderlich ist, sollten Sie daher Alternativen erwägen, wie z.B. [nachverfolgbare Entitäten](https://trackableentities.github.io/). Diese Technologie ähnelt den Entitäten mit Selbstnachverfolgung und wird von der Community aktiver entwickelt. Alternativ dazu können Sie auch benutzerdefinierten Code mithilfe von APIs auf niedriger Ebene zur Änderungsnachverfolgung schreiben.

Diese exemplarische Vorgehensweise veranschaulicht das Szenario, in dem ein Windows Communication Foundation (WCF)-Dienst einen Vorgang verfügbar macht, der ein Entitäts Diagramm zurückgibt. Anschließend bearbeitet eine Client Anwendung dieses Diagramm und übermittelt die Änderungen an einen Dienst Vorgang, der die Updates in einer Datenbank mithilfe Entity Framework überprüft und speichert.

Bevor Sie diese exemplarische Vorgehensweise durcharbeiten, sollten Sie die Seite [Entitäten mit selbst Nachverfolgung](xref:ef6/fundamentals/disconnected-entities/self-tracking-entities/index) lesen.

Diese exemplarische Vorgehensweise umfasst die folgenden Aktionen:

-   Erstellt eine Datenbank, auf die zugegriffen werden soll.
-   Erstellt eine Klassenbibliothek, die das Modell enthält.
-   Vertauscht zur Vorlage für Entitäts-Generator mit selbst Nachverfolgung.
-   Verschiebt die Entitäts Klassen in ein separates Projekt.
-   Erstellt einen WCF-Dienst, der Vorgänge zum Abfragen und Speichern von Entitäten verfügbar macht.
-   Erstellt Client Anwendungen (Konsole und WPF), die den-Dienst nutzen.

Wir verwenden Database First in dieser exemplarischen Vorgehensweise, aber dieselben Verfahren gelten auch für Model First.

## <a name="pre-requisites"></a>Voraussetzungen

Um diese exemplarische Vorgehensweise abzuschließen, benötigen Sie eine aktuelle Version von Visual Studio.

## <a name="create-a-database"></a>Erstellen einer Datenbank

Der Datenbankserver, der mit Visual Studio installiert wird, unterscheidet sich abhängig von der installierten Version von Visual Studio:

-   Wenn Sie Visual Studio 2012 verwenden, erstellen Sie eine localdb-Datenbank.
-   Wenn Sie Visual Studio 2010 verwenden, erstellen Sie eine SQL Express-Datenbank.

Nun generieren wir die Datenbank.

-   Öffnen Sie Visual Studio.
-   **Ansicht- &gt; Server-Explorer**
-   Klicken Sie mit der rechten Maustaste auf **Datenverbindungen- &gt; Verbindung hinzufügen...**
-   Wenn Sie über Server-Explorer keine Verbindung mit einer Datenbank hergestellt haben, müssen Sie **Microsoft SQL Server** als Datenquelle auswählen.
-   Stellen Sie eine Verbindung mit localdb oder SQL Express her, je nachdem, welche installiert wurde.
-   Geben Sie **stesample** als Datenbanknamen ein.
-   Wählen Sie **OK** aus, und Sie werden gefragt, ob Sie eine neue Datenbank erstellen möchten, und wählen Sie **Ja** aus.
-   Die neue Datenbank wird nun in Server-Explorer
-   Wenn Sie Visual Studio 2012 verwenden
    -   Klicken Sie im Server-Explorer mit der rechten Maustaste auf die Datenbank, und wählen Sie **Neue Abfrage** aus.
    -   Kopieren Sie den folgenden SQL-Befehl in die neue Abfrage, klicken Sie mit der rechten Maustaste auf die Abfrage, und wählen Sie **Ausführen** .
-   Wenn Sie Visual Studio 2010 verwenden
    -   Wählen Sie **Data- &gt; Transact SQL-Editor- &gt; neue Abfrage Verbindung...**
    -   Geben Sie ein **. \\ SQLExpress** als Servername, und klicken Sie auf **OK** .
    -   Wählen Sie in der Dropdown-Datei am oberen Rand des Abfrage-Editors die **stesample** -Datenbank aus.
    -   Kopieren Sie das folgende SQL in die neue Abfrage, klicken Sie mit der rechten Maustaste auf die Abfrage, und wählen Sie **SQL ausführen** aus.

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

    SET IDENTITY_INSERT [dbo].[Blogs] ON
    INSERT INTO [dbo].[Blogs] ([BlogId], [Name], [Url]) VALUES (1, N'ADO.NET Blog', N'blogs.msdn.com/adonet')
    SET IDENTITY_INSERT [dbo].[Blogs] OFF
    INSERT INTO [dbo].[Posts] ([Title], [Content], [BlogId]) VALUES (N'Intro to EF', N'Interesting stuff...', 1)
    INSERT INTO [dbo].[Posts] ([Title], [Content], [BlogId]) VALUES (N'What is New', N'More interesting stuff...', 1)
```

## <a name="create-the-model"></a>Erstellen des Modells

Zuerst wird ein Projekt benötigt, in das das Modell eingefügt werden kann.

-   **Datei- &gt; New- &gt; Project...**
-   Wählen Sie im linken Bereich **Visual C \# ** und dann **Klassenbibliothek** aus.
-   Geben Sie **stesample** als Name ein, und klicken Sie auf **OK** .

Im EF-Designer erstellen wir nun ein einfaches Modell für den Zugriff auf unsere Datenbank:

-   **Project- &gt; Neues Element hinzufügen...**
-   Wählen Sie im linken Bereich **Daten** aus, und klicken Sie dann auf **ADO.NET Entity Data Model**
-   Geben Sie als Name **bloggingmodel** ein, und klicken Sie auf **OK** .
-   Wählen Sie **aus Datenbank generieren aus** , und klicken Sie auf **weiter**
-   Geben Sie die Verbindungsinformationen für die Datenbank ein, die Sie im vorherigen Abschnitt erstellt haben.
-   Geben Sie **bloggingcontext** als Namen für die Verbindungs Zeichenfolge ein, und klicken Sie auf **weiter** .
-   Aktivieren Sie das Kontrollkästchen neben **Tabellen** und klicken Sie auf **Fertig** stellen.

## <a name="swap-to-ste-code-generation"></a>An ste-Code Generierung austauschen

Nun müssen wir die Standard Codegenerierung deaktivieren und in Entitäten mit selbst Nachverfolgung wechseln.

### <a name="if-you-are-using-visual-studio-2012"></a>Wenn Sie Visual Studio 2012 verwenden

-   Erweitern Sie in **Projektmappen-Explorer** den **Eintrag bloggingmodel. edmx** , und löschen Sie die **BloggingModel.tt** und **BloggingModel.Context.tt** 
     *dadurch wird die Standard Codegenerierung deaktiviert* .
-   Klicken Sie mit der rechten Maustaste auf einen leeren Bereich auf der EF-Designer Oberfläche, und wählen Sie **Code Generierungs Element hinzufügen aus.**
-   Wählen Sie im linken Bereich **Online** aus, und suchen Sie nach **ste Generator** .
-   Wählen Sie die Vorlage **ste Generator \# für C aus** , geben Sie **stetemplate** als Namen ein, und klicken Sie auf **Hinzufügen**
-   Die Dateien **STETemplate.tt** und **STETemplate.Context.tt** werden unter der Datei bloggingmodel. edmx hinzugefügt.

### <a name="if-you-are-using-visual-studio-2010"></a>Wenn Sie Visual Studio 2010 verwenden

-   Klicken Sie mit der rechten Maustaste auf einen leeren Bereich auf der EF-Designer Oberfläche, und wählen Sie **Code Generierungs Element hinzufügen aus.**
-   Wählen Sie im linken Bereich **Code** und dann **ADO.net Entitäts-Generator mit selbst Nachverfolgung** aus.
-   Geben Sie **stetemplate** als Name ein, und klicken Sie auf **Hinzufügen**
-   Die Dateien **STETemplate.tt** und **STETemplate.Context.tt** werden direkt dem Projekt hinzugefügt.

## <a name="move-entity-types-into-separate-project"></a>Verschieben von Entitäts Typen in ein separates Projekt

Zur Verwendung von Entitäten mit selbst Nachverfolgung benötigt unsere Client Anwendung Zugriff auf die Entitäts Klassen, die aus dem Modell generiert werden. Da wir das gesamte Modell nicht für die Client Anwendung verfügbar machen möchten, verschieben wir die Entitäts Klassen in ein separates Projekt.

Im ersten Schritt wird das Erstellen von Entitäts Klassen im vorhandenen Projekt beendet:

-   Klicken Sie in **Projektmappen-Explorer** mit der rechten Maustaste auf **STETemplate.tt** , und wählen Sie **Eigenschaften**
-   Löschen Sie im **Eigenschaften** Fenster **TextTemplatingFileGenerator** aus der **CustomTool** -Eigenschaft.
-   Erweitern Sie in **Projektmappen-Explorer** den Eintrag **STETemplate.tt** , und löschen Sie alle Dateien, die darin gespeichert sind

Als Nächstes fügen wir ein neues Projekt hinzu und generieren die Entitäts Klassen darin.

-   **Datei- &gt; Add- &gt; Project...**
-   Wählen Sie im linken Bereich **Visual C \# ** und dann **Klassenbibliothek** aus.
-   Geben Sie **stesample. Entities** als Name ein, und klicken Sie auf **OK** .
-   **Project- &gt; Vorhandenes Element hinzufügen...**
-   Navigieren Sie zum Projektordner " **stesample** ".
-   Wählen Sie diese Option aus, um **alle Dateien anzuzeigen \* . \* **
-   Wählen Sie die Datei **STETemplate.tt** aus.
-   Klicken Sie auf den Dropdown Pfeil neben der Schaltfläche **Hinzufügen** , und wählen Sie **als Link hinzufügen** aus.

    ![Verknüpfte Vorlage hinzufügen](~/ef6/media/addlinkedtemplate.png)

Wir stellen auch sicher, dass die Entitäts Klassen im gleichen Namespace wie der Kontext generiert werden. Dadurch wird lediglich die Anzahl der using-Anweisungen reduziert, die wir in der gesamten Anwendung hinzufügen müssen.

-   Klicken Sie in **Projektmappen-Explorer** mit der rechten Maustaste auf die verknüpfte **STETemplate.tt** , und wählen Sie **Eigenschaften**
-   Legen Sie im **Eigenschaften** Fenster **benutzerdefinierter Tool Namespace** auf **stesample** fest.

Der von der ste-Vorlage generierte Code benötigt einen Verweis auf " **System. Runtime. Serialization** ", damit er kompiliert werden kann. Diese Bibliothek wird für die WCF **DataContract** -und **DataMember** -Attribute benötigt, die für die serialisierbaren Entitäts Typen verwendet werden.

-   Klicken Sie in **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **stesample. Entities** , und wählen Sie **Verweis hinzufügen...**
    -   Aktivieren Sie in Visual Studio 2012 das Kontrollkästchen neben **System. Runtime. Serialization** , und klicken Sie auf **OK** .
    -   Wählen Sie in Visual Studio 2010 die Option **System. Runtime. Serialization** aus, und klicken Sie auf **OK** .

Schließlich benötigt das Projekt mit unserem Kontext darin einen Verweis auf die Entitäts Typen.

-   Klicken Sie in **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **stesample** , und wählen Sie **Verweis hinzufügen aus.**
    -   Wählen Sie in Visual Studio 2012 im linken Bereich Projekt Mappe aus, **Aktivieren Sie das** Kontrollkästchen neben **stesample. Entities** , und klicken Sie auf **OK** .
    -   Wählen Sie in Visual Studio 2010 die Registerkarte **Projekte** aus, wählen Sie **stesample. Entities** aus, und klicken Sie auf **OK** .

>[!NOTE]
> Eine weitere Möglichkeit zum Verschieben der Entitäts Typen in ein separates Projekt besteht darin, die Vorlagen Datei zu verschieben, anstatt Sie von Ihrem Standard Speicherort zu verknüpfen. Wenn Sie dies tun, müssen Sie die Variable " **inputFile** " in der Vorlage aktualisieren, um den relativen Pfad zur EDMX-Datei bereitzustellen (in diesem Beispiel: **. \\ Bloggingmodel. edmx**).

## <a name="create-a-wcf-service"></a>Erstellen eines WCF-Dienstanbieter

Nun ist es an der Zeit, einen WCF-Dienst hinzuzufügen, um unsere Daten verfügbar zu machen. wir beginnen mit der Erstellung des Projekts.

-   **Datei- &gt; Add- &gt; Project...**
-   Wählen Sie im linken Bereich **Visual C \# ** und dann **WCF-Dienst Anwendung** aus.
-   Geben Sie **stesample. Service** als Name ein, und klicken Sie auf **OK** .
-   Fügen Sie einen Verweis auf die **System. Data. Entity** -Assembly hinzu.
-   Hinzufügen eines Verweises auf die Projekte " **stesample** " und " **stesample. Entities** "

Wir müssen die EF-Verbindungs Zeichenfolge in dieses Projekt kopieren, damit Sie zur Laufzeit gefunden wird.

-   Öffnen Sie die **App.Config** -Datei für das **stesample **-Projekt, und kopieren Sie das **connectionStrings** -Element.
-   Fügen Sie das **connectionStrings** -Element als untergeordnetes Element des **Configuration** -Elements der **Web.Config** Datei im **stesample. Service** -Projekt ein.

Nun ist es an der Zeit, den eigentlichen Dienst zu implementieren.

-   Öffnen Sie **IService1.cs** , und ersetzen Sie den Inhalt durch den folgenden Code:

``` csharp
    using System.Collections.Generic;
    using System.ServiceModel;

    namespace STESample.Service
    {
        [ServiceContract]
        public interface IService1
        {
            [OperationContract]
            List<Blog> GetBlogs();

            [OperationContract]
            void UpdateBlog(Blog blog);
        }
    }
```

-   Öffnen Sie **Service1. svc** , und ersetzen Sie den Inhalt durch den folgenden Code.

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Data;
    using System.Linq;

    namespace STESample.Service
    {
        public class Service1 : IService1
        {
            /// <summary>
            /// Gets all the Blogs and related Posts.
            /// </summary>
            public List<Blog> GetBlogs()
            {
                using (BloggingContext context = new BloggingContext())
                {
                    return context.Blogs.Include("Posts").ToList();
                }
            }

            /// <summary>
            /// Updates Blog and its related Posts.
            /// </summary>
            public void UpdateBlog(Blog blog)
            {
                using (BloggingContext context = new BloggingContext())
                {
                    try
                    {
                        // TODO: Perform validation on the updated order before applying the changes.

                        // The ApplyChanges method examines the change tracking information
                        // contained in the graph of self-tracking entities to infer the set of operations
                        // that need to be performed to reflect the changes in the database.
                        context.Blogs.ApplyChanges(blog);
                        context.SaveChanges();

                    }
                    catch (UpdateException)
                    {
                        // To avoid propagating exception messages that contain sensitive data to the client tier
                        // calls to ApplyChanges and SaveChanges should be wrapped in exception handling code.
                        throw new InvalidOperationException("Failed to update. Try your request again.");
                    }
                }
            }        
        }
    }
```

## <a name="consume-the-service-from-a-console-application"></a>Nutzen des dienstanwendungs aus einer Konsolenanwendung

Wir erstellen eine Konsolenanwendung, die unseren Dienst verwendet.

-   **Datei- &gt; New- &gt; Project...**
-   Wählen Sie im linken Bereich **Visual C \# ** und dann **Konsolenanwendung** aus.
-   Geben Sie **stesample. consoletest** als Name ein, und klicken Sie auf **OK** .
-   Hinzufügen eines Verweises auf das Projekt " **stesample. Entities** "

Wir benötigen einen Dienst Verweis auf den WCF-Dienst.

-   Klicken Sie in **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **stesample. consoletest** , und wählen Sie **Dienstverweis hinzufügen...**
-   Klicken Sie auf **Ermitteln.**
-   Geben Sie **bloggingservice** als Namespace ein, und klicken Sie auf **OK** .

Nun können wir Code schreiben, um den Dienst zu nutzen.

-   Öffnen Sie **Program.cs** , und ersetzen Sie den Inhalt durch den folgenden Code.

``` csharp
    using STESample.ConsoleTest.BloggingService;
    using System;
    using System.Linq;

    namespace STESample.ConsoleTest
    {
        class Program
        {
            static void Main(string[] args)
            {
                // Print out the data before we change anything
                Console.WriteLine("Initial Data:");
                DisplayBlogsAndPosts();

                // Add a new Blog and some Posts
                AddBlogAndPost();
                Console.WriteLine("After Adding:");
                DisplayBlogsAndPosts();

                // Modify the Blog and one of its Posts
                UpdateBlogAndPost();
                Console.WriteLine("After Update:");
                DisplayBlogsAndPosts();

                // Delete the Blog and its Posts
                DeleteBlogAndPost();
                Console.WriteLine("After Delete:");
                DisplayBlogsAndPosts();

                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();
            }

            static void DisplayBlogsAndPosts()
            {
                using (var service = new Service1Client())
                {
                    // Get all Blogs (and Posts) from the service
                    // and print them to the console
                    var blogs = service.GetBlogs();
                    foreach (var blog in blogs)
                    {
                        Console.WriteLine(blog.Name);
                        foreach (var post in blog.Posts)
                        {
                            Console.WriteLine(" - {0}", post.Title);
                        }
                    }
                }

                Console.WriteLine();
                Console.WriteLine();
            }

            static void AddBlogAndPost()
            {
                using (var service = new Service1Client())
                {
                    // Create a new Blog with a couple of Posts
                    var newBlog = new Blog
                    {
                        Name = "The New Blog",
                        Posts =
                        {
                            new Post { Title = "Welcome to the new blog"},
                            new Post { Title = "What's new on the new blog"}
                        }
                    };

                    // Save the changes using the service
                    service.UpdateBlog(newBlog);
                }
            }

            static void UpdateBlogAndPost()
            {
                using (var service = new Service1Client())
                {
                    // Get all the Blogs
                    var blogs = service.GetBlogs();

                    // Use LINQ to Objects to find The New Blog
                    var blog = blogs.First(b => b.Name == "The New Blog");

                    // Update the Blogs name
                    blog.Name = "The Not-So-New Blog";

                    // Update one of the related posts
                    blog.Posts.First().Content = "Some interesting content...";

                    // Save the changes using the service
                    service.UpdateBlog(blog);
                }
            }

            static void DeleteBlogAndPost()
            {
                using (var service = new Service1Client())
                {
                    // Get all the Blogs
                    var blogs = service.GetBlogs();

                    // Use LINQ to Objects to find The Not-So-New Blog
                    var blog = blogs.First(b => b.Name == "The Not-So-New Blog");

                    // Mark all related Posts for deletion
                    // We need to call ToList because each Post will be removed from the
                    // Posts collection when we call MarkAsDeleted
                    foreach (var post in blog.Posts.ToList())
                    {
                        post.MarkAsDeleted();
                    }

                    // Mark the Blog for deletion
                    blog.MarkAsDeleted();

                    // Save the changes using the service
                    service.UpdateBlog(blog);
                }
            }
        }
    }
```

Sie können die Anwendung jetzt ausführen, um sie in Aktion zu sehen.

-   Klicken Sie mit der rechten Maustaste auf das Projekt **stesample. consoletest** in **Projektmappen-Explorer** und wählen Sie **Debuggen &gt; neue Instanz starten** aus.

Die folgende Ausgabe wird angezeigt, wenn die Anwendung ausgeführt wird.

```console
Initial Data:
ADO.NET Blog
- Intro to EF
- What is New

After Adding:
ADO.NET Blog
- Intro to EF
- What is New
The New Blog
- Welcome to the new blog
- What's new on the new blog

After Update:
ADO.NET Blog
- Intro to EF
- What is New
The Not-So-New Blog
- Welcome to the new blog
- What's new on the new blog

After Delete:
ADO.NET Blog
- Intro to EF
- What is New

Press any key to exit...
```

## <a name="consume-the-service-from-a-wpf-application"></a>Nutzen des dienstanwendungs aus einer WPF-Anwendung

Erstellen Sie eine WPF-Anwendung, die unseren Dienst verwendet.

-   **Datei- &gt; New- &gt; Project...**
-   Wählen Sie im linken Bereich **Visual C \# ** und dann **WPF-Anwendung** aus.
-   Geben Sie **stesample. wpftest** als Name ein, und klicken Sie auf **OK** .
-   Hinzufügen eines Verweises auf das Projekt " **stesample. Entities** "

Wir benötigen einen Dienst Verweis auf den WCF-Dienst.

-   Klicken Sie in **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **stesample. wpftest** , und wählen Sie **Dienstverweis hinzufügen...**
-   Klicken Sie auf **Ermitteln.**
-   Geben Sie **bloggingservice** als Namespace ein, und klicken Sie auf **OK** .

Nun können wir Code schreiben, um den Dienst zu nutzen.

-   Öffnen Sie " **MainWindow. XAML** ", und ersetzen Sie den Inhalt durch den folgenden Code.

``` xaml
    <Window
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:STESample="clr-namespace:STESample;assembly=STESample.Entities"
        mc:Ignorable="d" x:Class="STESample.WPFTest.MainWindow"
        Title="MainWindow" Height="350" Width="525" Loaded="Window_Loaded">

        <Window.Resources>
            <CollectionViewSource
                x:Key="blogViewSource"
                d:DesignSource="{d:DesignInstance {x:Type STESample:Blog}, CreateList=True}"/>
            <CollectionViewSource
                x:Key="blogPostsViewSource"
                Source="{Binding Posts, Source={StaticResource blogViewSource}}"/>
        </Window.Resources>

        <Grid DataContext="{StaticResource blogViewSource}">
            <DataGrid AutoGenerateColumns="False" EnableRowVirtualization="True"
                      ItemsSource="{Binding}" Margin="10,10,10,179">
                <DataGrid.Columns>
                    <DataGridTextColumn Binding="{Binding BlogId}" Header="Id" Width="Auto" IsReadOnly="True" />
                    <DataGridTextColumn Binding="{Binding Name}" Header="Name" Width="Auto"/>
                    <DataGridTextColumn Binding="{Binding Url}" Header="Url" Width="Auto"/>
                </DataGrid.Columns>
            </DataGrid>
            <DataGrid AutoGenerateColumns="False" EnableRowVirtualization="True"
                      ItemsSource="{Binding Source={StaticResource blogPostsViewSource}}" Margin="10,145,10,38">
                <DataGrid.Columns>
                    <DataGridTextColumn Binding="{Binding PostId}" Header="Id" Width="Auto"  IsReadOnly="True"/>
                    <DataGridTextColumn Binding="{Binding Title}" Header="Title" Width="Auto"/>
                    <DataGridTextColumn Binding="{Binding Content}" Header="Content" Width="Auto"/>
                </DataGrid.Columns>
            </DataGrid>
            <Button Width="68" Height="23" HorizontalAlignment="Right" VerticalAlignment="Bottom"
                    Margin="0,0,10,10" Click="buttonSave_Click">Save</Button>
        </Grid>
    </Window>
```

-   Öffnen Sie den Code Behind für MainWindow (**MainWindow.XAML.cs**), und ersetzen Sie den Inhalt durch den folgenden Code.

``` csharp
    using STESample.WPFTest.BloggingService;
    using System.Collections.Generic;
    using System.Linq;
    using System.Windows;
    using System.Windows.Data;

    namespace STESample.WPFTest
    {
        public partial class MainWindow : Window
        {
            public MainWindow()
            {
                InitializeComponent();
            }

            private void Window_Loaded(object sender, RoutedEventArgs e)
            {
                using (var service = new Service1Client())
                {
                    // Find the view source for Blogs and populate it with all Blogs (and related Posts)
                    // from the Service. The default editing functionality of WPF will allow the objects
                    // to be manipulated on the screen.
                    var blogsViewSource = (CollectionViewSource)this.FindResource("blogViewSource");
                    blogsViewSource.Source = service.GetBlogs().ToList();
                }
            }

            private void buttonSave_Click(object sender, RoutedEventArgs e)
            {
                using (var service = new Service1Client())
                {
                    // Get the blogs that are bound to the screen
                    var blogsViewSource = (CollectionViewSource)this.FindResource("blogViewSource");
                    var blogs = (List<Blog>)blogsViewSource.Source;

                    // Save all Blogs and related Posts
                    foreach (var blog in blogs)
                    {
                        service.UpdateBlog(blog);
                    }

                    // Re-query for data to get database-generated keys etc.
                    blogsViewSource.Source = service.GetBlogs().ToList();
                }
            }
        }
    }
```

Sie können die Anwendung jetzt ausführen, um sie in Aktion zu sehen.

-   Klicken Sie mit der rechten Maustaste auf das Projekt **stesample. wpftest** in **Projektmappen-Explorer** und wählen Sie **Debuggen &gt; neue Instanz starten** aus.
-   Sie können die Daten mithilfe des Bildschirms bearbeiten und mithilfe der Schaltfläche " **Speichern** " über den Dienst speichern.

![WPF-Hauptfenster](~/ef6/media/wpf.png)
