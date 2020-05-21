---
title: Datenbindung mit WPF-EF6
author: divega
ms.date: 05/19/2020
ms.assetid: e90d48e6-bea7785-47ef-b756-7b89cce4daf0
ms.openlocfilehash: c8e9e9a0810d8192e5184abbc7df2e74e4ee7edb
ms.sourcegitcommit: 59e3d5ce7dfb284457cf1c991091683b2d1afe9d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83672909"
---
# <a name="databinding-with-wpf"></a>Datenbindung mit WPF

> [!IMPORTANT]
> **Dieses Dokument gilt nur für WPF auf dem .NET Framework**
>
> In diesem Dokument wird das Datenbindung für WPF auf dem .NET Framework beschrieben. Für neue .net Core-Projekte empfiehlt es sich, anstelle von Entity Framework 6 [EF Core](/ef/core) zu verwenden. Die Dokumentation für Datenbindung in EF Core wird in der [Problem #778](https://github.com/dotnet/EntityFramework.Docs/issues/778)nachverfolgt.

In dieser schrittweisen exemplarischen Vorgehensweise wird gezeigt, wie poco-Typen an WPF-Steuerelemente in einem Master-Detail-Formular gebunden werden. Die Anwendung verwendet die Entity Framework-APIs zum Auffüllen von Objekten mit Daten aus der Datenbank, zum Nachverfolgen von Änderungen und zum Beibehalten von Daten in der Datenbank.

Das Modell definiert zwei Typen, die an einer 1: n-Beziehung beteiligt sind: **Kategorie** (Prinzipal \\ Master) und **Produkt** (abhängige \\ Details). Anschließend werden die Visual Studio-Tools verwendet, um die im Modell definierten Typen an die WPF-Steuerelemente zu binden. Das WPF-Daten Bindungs Framework ermöglicht die Navigation zwischen verbundenen Objekten: durch Auswählen von Zeilen in der Master Ansicht wird die Detailansicht mit den entsprechenden untergeordneten Daten aktualisiert.

Die Screenshots und Code Auflistungen in dieser exemplarischen Vorgehensweise werden aus Visual Studio 2013 erstellt, aber Sie können diese exemplarische Vorgehensweise mit Visual Studio 2012 oder Visual Studio 2010 durcharbeiten.

## <a name="use-the-object-option-for-creating-wpf-data-sources"></a>Verwenden Sie die Option "Object" zum Erstellen von WPF-Datenquellen.

Mit der vorherigen Version von Entity Framework wir die Verwendung der **Daten Bank** Option empfohlen, wenn eine neue Datenquelle basierend auf einem Modell erstellt wird, das mit dem EF-Designer erstellt wurde. Der Grund hierfür ist, dass der Designer einen Kontext generieren würde, der von ObjectContext-und Entitäts Klassen abgeleitet wurde, die von EntityObject abgeleitet wurden. Wenn Sie die-Datenbankoption verwenden, können Sie den besten Code für die Interaktion mit dieser API-Oberfläche schreiben.

Die EF-Designer für Visual Studio 2012 und Visual Studio 2013 generieren einen Kontext, der von dbcontext zusammen mit einfachen poco-Entitäts Klassen abgeleitet wird. In Visual Studio 2010 empfiehlt es sich, auf eine Code Generierungs Vorlage zu wechseln, die dbcontext verwendet, wie weiter unten in dieser exemplarischen Vorgehensweise beschrieben.

Wenn Sie die dbcontext-API-Oberfläche verwenden, sollten Sie beim Erstellen einer neuen Datenquelle die- **Objekt** Option verwenden, wie in dieser exemplarischen Vorgehensweise dargestellt.

Bei Bedarf können Sie die [auf ObjectContext basierende Codegenerierung](~/ef6/modeling/designer/codegen/legacy-objectcontext.md) für Modelle zurücksetzen, die mit dem EF-Designer erstellt wurden.

## <a name="pre-requisites"></a>Voraussetzungen

Zum Durchführen dieser exemplarischen Vorgehensweise muss Visual Studio 2013, Visual Studio 2012 oder Visual Studio 2010 installiert sein.

Wenn Sie Visual Studio 2010 verwenden, müssen Sie auch nuget installieren. Weitere Informationen finden Sie unter [Installieren von nuget](https://docs.microsoft.com/nuget/install-nuget-client-tools).  

## <a name="create-the-application"></a>Erstellen der Anwendung

-   Öffnen Sie Visual Studio.
-   **Datei- &gt; New- &gt; Project...**
-   Klicken **Windows**Sie   im linken Bereich auf Fenster und im rechten Bereich auf **wpfapplikation** .
-   Geben Sie **wpfwiamsample**   als Name ein.
-    **OK** auswählen

## <a name="install-the-entity-framework-nuget-package"></a>Installieren des Entity Framework nuget-Pakets

-   Klicken Sie in Projektmappen-Explorer mit der rechten Maustaste auf das **winformswideratsample** -Projekt.
-   Wählen Sie **nuget-Pakete verwalten... aus.**
-   Wählen Sie im Dialogfeld nuget-Pakete verwalten die Registerkarte **Online** aus, und wählen Sie das Paket **EntityFramework** aus.
-   Klicken Sie auf **Install** (Installieren).  
    >[!NOTE]
    > Zusätzlich zur EntityFramework-Assembly wird auch ein Verweis auf "System. ComponentModel. DataAnnotations" hinzugefügt. Wenn das Projekt einen Verweis auf System. Data. Entity enthält, wird es entfernt, wenn das EntityFramework-Paket installiert wird. Die System. Data. Entity-Assembly wird für Entity Framework 6-Anwendungen nicht mehr verwendet.

## <a name="define-a-model"></a>Definieren eines Modells

In dieser exemplarischen Vorgehensweise können Sie ein Modell mithilfe von Code First oder dem EF-Designer implementieren. Führen Sie einen der beiden folgenden Abschnitte aus.

### <a name="option-1-define-a-model-using-code-first"></a>Option 1: Definieren eines Modells mit Code First

In diesem Abschnitt wird gezeigt, wie ein Modell und die zugehörige Datenbank mithilfe Code First erstellt werden. Fahren Sie mit dem nächsten Abschnitt fort (**Option 2: Definieren eines Modells mithilfe Database First)** , wenn Sie stattdessen Database First verwenden möchten, um das Modell aus einer Datenbank mit dem EF-Designer umzukehren.

Wenn Sie Code First Entwicklung verwenden, schreiben Sie in der Regel .NET Framework Klassen, die ihr konzeptionelles (Domänen-) Modell definieren.

-   Fügen Sie der **wpfwiderfsample** -Klasse eine neue Klasse hinzu:
    -   Klicken Sie mit der rechten Maustaste auf den Projektnamen.
    -   Wählen Sie **Hinzufügen**und dann **Neues Element** aus.
    -   Wählen Sie **Klasse** aus, und geben Sie **Product**als   Klassennamen ein.
-   Ersetzen Sie die **Product**   Class-Definition durch den folgenden Code:

``` csharp
    namespace WPFwithEFSample
    {
        public class Product
        {
            public int ProductId { get; set; }
            public string Name { get; set; }

            public int CategoryId { get; set; }
            public virtual Category Category { get; set; }
        }
    }

-   Add a **Category** class with the following definition:

    using System.Collections.ObjectModel;

    namespace WPFwithEFSample
    {
        public class Category
        {
            public Category()
            {
                this.Products = new ObservableCollection<Product>();
            }

            public int CategoryId { get; set; }
            public string Name { get; set; }

            public virtual ObservableCollection<Product> Products { get; private set; }
        }
    }
```

Die **Products** -Eigenschaft der **Category** -Klasse und der **Category** -Eigenschaft in der **Product** -Klasse sind Navigations Eigenschaften. In Entity Framework bieten Navigations Eigenschaften eine Möglichkeit, eine Beziehung zwischen zwei Entitäts Typen zu navigieren.

Zusätzlich zum Definieren von Entitäten müssen Sie eine Klasse definieren, die von dbcontext abgeleitet ist und dbset &lt; TEntity-Eigenschaften verfügbar macht &gt; . Die dbset &lt; TEntity- &gt; Eigenschaften lassen den Kontext wissen, welche Typen Sie in das Modell einschließen möchten.

Eine Instanz des abgeleiteten dbcontext-Typs verwaltet die Entitäts Objekte zur Laufzeit. dazu gehören das Auffüllen von Objekten mit Daten aus einer Datenbank, die Änderungs Nachverfolgung und das Beibehalten von Daten in der Datenbank.

-   Fügen Sie dem Projekt eine neue **productcontext** -Klasse mit der folgenden Definition hinzu:

``` csharp
    using System.Data.Entity;

    namespace WPFwithEFSample
    {
        public class ProductContext : DbContext
        {
            public DbSet<Category> Categories { get; set; }
            public DbSet<Product> Products { get; set; }
        }
    }
```

Kompilieren Sie das Projekt.

### <a name="option-2-define-a-model-using-database-first"></a>Option 2: Definieren eines Modells mit Database First

In diesem Abschnitt wird gezeigt, wie Sie mit Database First das Modell aus einer Datenbank mit dem EF-Designer rückgängig machen. Wenn Sie den vorherigen Abschnitt (**Option 1: Definieren eines Modells mit Code First)** abgeschlossen haben, überspringen Sie diesen Abschnitt, und wechseln Sie direkt zum Abschnitt **Lazy Load** .

#### <a name="create-an-existing-database"></a>Erstellen einer vorhandenen Datenbank

Wenn Sie eine vorhandene Datenbank als Ziel haben, wird Sie in der Regel bereits erstellt, aber in dieser exemplarischen Vorgehensweise müssen wir eine Datenbank erstellen, auf die zugegriffen werden kann.

Der Datenbankserver, der mit Visual Studio installiert wird, unterscheidet sich abhängig von der installierten Version von Visual Studio:

-   Wenn Sie Visual Studio 2010 verwenden, erstellen Sie eine SQL Express-Datenbank.
-   Wenn Sie Visual Studio 2012 verwenden, erstellen Sie eine [localdb](https://msdn.microsoft.com/library/hh510202.aspx) -Datenbank.

Nun generieren wir die Datenbank.

-   **Ansicht- &gt; Server-Explorer**
-   Klicken Sie mit der rechten Maustaste auf **Datenverbindungen- &gt; Verbindung hinzufügen...**
-   Wenn Sie über Server-Explorer keine Verbindung mit einer Datenbank hergestellt haben, müssen Sie Microsoft SQL Server als Datenquelle auswählen.

    ![Datenquelle ändern](~/ef6/media/changedatasource.png)

-   Stellen Sie abhängig von der installierten Verbindung entweder localdb oder SQL Express her, und geben Sie **Produkte** als Datenbanknamen ein.

    ![Verbindung localdb hinzufügen](~/ef6/media/addconnectionlocaldb.png)

    ![Verbindung hinzufügen Express](~/ef6/media/addconnectionexpress.png)

-   Wählen Sie **OK** aus, und Sie werden gefragt, ob Sie eine neue Datenbank erstellen möchten, und wählen Sie **Ja** aus.

    ![Erstellen einer Datenbank](~/ef6/media/createdatabase.png)

-   Die neue Datenbank wird nun in Server-Explorer angezeigt, klicken Sie mit der rechten Maustaste darauf, und wählen Sie **neue Abfrage** aus.
-   Kopieren Sie den folgenden SQL-Befehl in die neue Abfrage, klicken Sie mit der rechten Maustaste auf die Abfrage, und wählen Sie **Ausführen** .

``` SQL
    CREATE TABLE [dbo].[Categories] (
        [CategoryId] [int] NOT NULL IDENTITY,
        [Name] [nvarchar](max),
        CONSTRAINT [PK_dbo.Categories] PRIMARY KEY ([CategoryId])
    )

    CREATE TABLE [dbo].[Products] (
        [ProductId] [int] NOT NULL IDENTITY,
        [Name] [nvarchar](max),
        [CategoryId] [int] NOT NULL,
        CONSTRAINT [PK_dbo.Products] PRIMARY KEY ([ProductId])
    )

    CREATE INDEX [IX_CategoryId] ON [dbo].[Products]([CategoryId])

    ALTER TABLE [dbo].[Products] ADD CONSTRAINT [FK_dbo.Products_dbo.Categories_CategoryId] FOREIGN KEY ([CategoryId]) REFERENCES [dbo].[Categories] ([CategoryId]) ON DELETE CASCADE
```

#### <a name="reverse-engineer-model"></a>Reverse Engineering-Modell

Wir verwenden Entity Framework Designer, die als Teil von Visual Studio enthalten ist, um das Modell zu erstellen.

-   **Project- &gt; Neues Element hinzufügen...**
-   Wählen Sie im linken Menü **Daten** aus, und klicken Sie dann auf **ADO.NET Entity Data Model**
-   Geben Sie **ProductModel** als Name ein, und klicken Sie auf **OK** .
-   Dadurch wird der **Entity Data Model-Assistent** gestartet.
-   Wählen Sie **aus Datenbank generieren aus** , und klicken Sie auf **weiter**

    ![Auswählen von Modellinhalten](~/ef6/media/choosemodelcontents.png)

-   Wählen Sie die Verbindung mit der Datenbank, die Sie im ersten Abschnitt erstellt haben, geben Sie **productcontext** als Namen der Verbindungs Zeichenfolge ein, und klicken Sie auf **weiter** .

    ![Verbindung auswählen](~/ef6/media/chooseyourconnection.png)

-   Aktivieren Sie das Kontrollkästchen neben "Tables", um alle Tabellen zu importieren, und klicken Sie auf "Fertigstellen"

    ![Objekte auswählen](~/ef6/media/chooseyourobjects.png)

Sobald der Reverse-Engineering-Prozess abgeschlossen ist, wird das neue Modell dem Projekt hinzugefügt und geöffnet, damit Sie es im Entity Framework Designer anzeigen können. Eine APP. config-Datei wurde dem Projekt auch mit den Verbindungsdetails für die Datenbank hinzugefügt.

#### <a name="additional-steps-in-visual-studio-2010"></a>Weitere Schritte in Visual Studio 2010

Wenn Sie in Visual Studio 2010 arbeiten, müssen Sie den EF-Designer aktualisieren, damit die EF6-Codegenerierung verwendet werden kann.

-   Klicken Sie mit der rechten Maustaste auf eine leere Stelle des Modells im EF-Designer, und wählen Sie **Code Generierungs Element hinzufügen... aus.**
-   Wählen Sie im linken Menü **Online Vorlagen** aus, und suchen Sie nach **dbcontext** .
-   Wählen Sie den **EF 6. x dbcontext Generator für C aus \# ,** geben Sie **ProductModel** als Name ein, und klicken Sie auf hinzufügen.

#### <a name="updating-code-generation-for-data-binding"></a>Aktualisieren der Codegenerierung für die Datenbindung

EF generiert Code aus dem Modell mithilfe von T4-Vorlagen. Die Vorlagen, die in Visual Studio enthalten sind oder aus der Visual Studio Gallery heruntergeladen wurden, sind für die allgemeine Verwendung vorgesehen. Dies bedeutet, dass die von diesen Vorlagen generierten Entitäten einfache ICollection &lt; T- &gt; Eigenschaften haben. Wenn Sie jedoch die Datenbindung mithilfe von WPF durchführen, ist es wünschenswert, **ObservableCollection** für Auflistungs Eigenschaften zu verwenden, damit WPF Änderungen an den Auflistungen nachverfolgen kann. Zu diesem Zweck werden die Vorlagen für die Verwendung von ObservableCollection geändert.

-   Öffnen Sie die **Projektmappen-Explorer** , und suchen Sie nach der Datei **ProductModel. edmx.**
-   Suchen Sie die **ProductModel.tt** -Datei, die unter der Datei "ProductModel. edmx" eingefügt wird.

    ![WPF-Produktmodell Vorlage](~/ef6/media/wpfproductmodeltemplate.png)

-   Doppelklicken Sie auf die Datei ProductModel.tt, um Sie im Visual Studio-Editor zu öffnen.
-   Suchen und ersetzen Sie die beiden Vorkommen von "**ICollection**" durch "**ObservableCollection**". Diese befinden sich ungefähr in den Zeilen 296 und 484.
-   Suchen und ersetzen Sie das erste Vorkommen von "**HashSet**" durch "**ObservableCollection**". Dieses Vorkommen befindet sich ungefähr in Zeile 50. Ersetzen **Sie nicht** das zweite Vorkommen des Hashsets, das später im Code gefunden wurde.
-   Suchen und ersetzen Sie das einzige Vorkommen von "**System. Collections. Generic**" durch "**System. Collections. ObjectModel**". Dies befindet sich ungefähr in Zeile 424.
-   Speichern Sie die Datei ProductModel.tt. Dies sollte bewirken, dass der Code für Entitäten erneut generiert wird. Wenn der Code nicht automatisch neu generiert wird, klicken Sie mit der rechten Maustaste auf ProductModel.tt, und wählen Sie "benutzerdefiniertes Tool ausführen".

Wenn Sie nun die Category.cs-Datei öffnen (die unter ProductModel.tt geschachtelte ist), sollten Sie sehen, dass die Products-Sammlung den Typ **ObservableCollection- &lt; Produkt &gt; **aufweist.

Kompilieren Sie das Projekt.

## <a name="lazy-loading"></a>Lazy Loading

Die **Products** -Eigenschaft der **Category** -Klasse und der **Category** -Eigenschaft in der **Product** -Klasse sind Navigations Eigenschaften. In Entity Framework bieten Navigations Eigenschaften eine Möglichkeit, eine Beziehung zwischen zwei Entitäts Typen zu navigieren.

EF bietet Ihnen die Möglichkeit, Verwandte Entitäten aus der Datenbank automatisch zu laden, wenn Sie zum ersten Mal auf die Navigations Eigenschaft zugreifen. Beachten Sie bei dieser Art von laden (Lazy Loading), dass beim ersten Zugriff auf jede Navigations Eigenschaft eine separate Abfrage für die Datenbank ausgeführt wird, wenn der Inhalt nicht bereits im Kontext vorhanden ist.

Wenn poco-Entitäts Typen verwendet werden, erreicht EF Lazy Loading durch Erstellen von Instanzen abgeleiteter Proxy Typen während der Laufzeit und das anschließende Überschreiben virtueller Eigenschaften in den Klassen, um den Lade Hook hinzuzufügen. Wenn Sie Lazy Loading verknüpfter Objekte abrufen möchten, müssen Sie die Navigations Eigenschaften Getter als **Public** und **Virtual** (**Overridable** in Visual Basic) deklarieren, und die Klasse darf nicht **versiegelt** sein (**nodeverridable** in Visual Basic). Bei Verwendung Database First Navigations Eigenschaften automatisch als virtuell fest, um Lazy Loading zu aktivieren. Im Code First Abschnitt haben wir entschieden, die Navigations Eigenschaften aus demselben Grund virtuell zu machen.

## <a name="bind-object-to-controls"></a>Binden von Objekten an Steuerelemente

Fügen Sie die Klassen, die im Modell definiert sind, als Datenquellen für diese WPF-Anwendung hinzu.

-   Doppelklicken Sie in Projektmappen-Explorer auf die Datei **MainWindow. XAML** , um das Hauptformular zu öffnen.
-   Wählen Sie im Hauptmenü **Projekt- &gt; neue Datenquelle hinzufügen... aus.**
    (in Visual Studio 2010 müssen Sie **Daten- &gt; neue Datenquelle hinzufügen...**)
-   Wählen Sie im Fenster Datenquelle auswählen die Option **Objekt** aus, und klicken Sie auf **weiter** .
-   Erweitern Sie im Dialogfeld Wählen Sie die Datenobjekte aus das **wpfwiderf-Beispiel**   zweimal, und wählen Sie **Category** aus.  
    *Die **Produkt** Datenquelle muss nicht ausgewählt werden, da wir Sie über die **Produkt**Eigenschaft der **Kategorie** Datenquelle erhalten.*  

    ![Datenobjekte auswählen](~/ef6/media/selectdataobjects.png)

-   Klicken Sie auf **Fertig stellen**.
-   Das Fenster Datenquellen wird neben dem Fenster MainWindow. XAML geöffnet *, wenn das Fenster Datenquellen nicht angezeigt wird, wählen Sie **Ansicht- &gt; Weitere Windows- &gt; Datenquellen** * aus.
-   Klicken Sie auf das anheft Symbol, damit das Fenster Datenquellen nicht automatisch ausgeblendet wird. Sie müssen möglicherweise auf die Schaltfläche "Aktualisieren" klicken, wenn das Fenster bereits sichtbar war.

    ![Datenquellen](~/ef6/media/datasources.png)

-   Wählen Sie die **kategoriedatenquelle** aus, und ziehen Sie Sie auf das Formular.

Folgendes ist aufgetreten, als wir diese Quelle gezogen haben:

-   Die **categoryviewsource** -Ressource und das **categorydatagrid-** Steuerelement wurden zu XAML hinzugefügt. 
-   Die DataContext-Eigenschaft des übergeordneten Grid-Elements wurde auf "{statikresource **categoryviewsource** }" festgelegt.Die **categoryviewsource** -Ressource dient als Bindungs Quelle für das äußere über \\ geordnete Raster Element. Die inneren Raster Elemente erben dann den DataContext-Wert vom übergeordneten Raster (die ItemsSource-Eigenschaft von categorydatagrid ist auf "{Binding}" festgelegt).

``` xml
    <Window.Resources>
        <CollectionViewSource x:Key="categoryViewSource"
                                d:DesignSource="{d:DesignInstance {x:Type local:Category}, CreateList=True}"/>
    </Window.Resources>
    <Grid DataContext="{StaticResource categoryViewSource}">
        <DataGrid x:Name="categoryDataGrid" AutoGenerateColumns="False" EnableRowVirtualization="True"
                    ItemsSource="{Binding}" Margin="13,13,43,191"
                    RowDetailsVisibilityMode="VisibleWhenSelected">
            <DataGrid.Columns>
                <DataGridTextColumn x:Name="categoryIdColumn" Binding="{Binding CategoryId}"
                                    Header="Category Id" Width="SizeToHeader"/>
                <DataGridTextColumn x:Name="nameColumn" Binding="{Binding Name}"
                                    Header="Name" Width="SizeToHeader"/>
            </DataGrid.Columns>
        </DataGrid>
    </Grid>
```

## <a name="adding-a-details-grid"></a>Hinzufügen eines Detail Rasters

Nachdem wir nun über ein Raster zum Anzeigen von Kategorien verfügen, können wir ein Detail Raster hinzufügen, um die zugeordneten Produkte anzuzeigen.

-   Wählen Sie unter der **Kategorie** Datenquelle die Eigenschaft **Produkte** aus, und ziehen Sie Sie auf das Formular.
    -   Die **categoryproductviewsource** -Ressource und das **productdatagrid-** Raster werden XAML hinzugefügt.
    -   Der Bindungs Pfad für diese Ressource ist auf Products festgelegt.
    -   WPF-Daten Bindungs Framework stellt sicher, dass nur Produkte im Zusammenhang mit der ausgewählten Kategorie in **productdatagrid** angezeigt werden.
-   Ziehen Sie die **Schaltfläche** aus der Toolbox auf das Formular. Legen Sie die **Name** -Eigenschaft auf **ButtonSave** und die **Content** -Eigenschaft auf **Save**fest.

Das Formular sollte in etwa wie folgt aussehen:

![Designer](~/ef6/media/designer.png) 

## <a name="add-code-that-handles-data-interaction"></a>Hinzufügen von Code, der die Daten Interaktion verarbeitet

Es ist an der Zeit, dem Hauptfenster einige Ereignishandler hinzuzufügen.

-   Klicken Sie im XAML-Fenster auf das ** &lt; Fenster** Element, sodass das Hauptfenster ausgewählt wird.
-   Wählen Sie im **Eigenschaften** Fenster oben rechts **Ereignisse** aus, und doppelklicken Sie dann auf das Textfeld rechts neben der **geladenen** Bezeichnung.

    ![Hauptfenster Eigenschaften](~/ef6/media/mainwindowproperties.png)

-   Fügen Sie auch das Click-Ereignis für die Schaltfläche **Speichern** hinzu, indem **Sie** im Designer auf die Schaltfläche Speichern doppelklicken. 

Dadurch gelangen Sie zum Code Behind für das Formular. nun bearbeiten wir den Code so, dass der productcontext zum Ausführen von Datenzugriff verwendet wird. Aktualisieren Sie den Code für das MainWindow, wie unten gezeigt.

Der Code deklariert eine Instanz von **productcontext**mit langer Laufzeit. Das **productcontext** -Objekt wird verwendet, um Daten abzufragen und in der Datenbank zu speichern. Der Löschvorgang **()** auf der **productcontext** -Instanz wird dann von der überschriebenen **OnClosing** -Methode aufgerufen.Die Code Kommentare enthalten Details zum Funktionsumfang des Codes.

``` csharp
    using System.Data.Entity;
    using System.Linq;
    using System.Windows;

    namespace WPFwithEFSample
    {
        public partial class MainWindow : Window
        {
            private ProductContext _context = new ProductContext();
            public MainWindow()
            {
                InitializeComponent();
            }

            private void Window_Loaded(object sender, RoutedEventArgs e)
            {
                System.Windows.Data.CollectionViewSource categoryViewSource =
                    ((System.Windows.Data.CollectionViewSource)(this.FindResource("categoryViewSource")));

                // Load is an extension method on IQueryable,
                // defined in the System.Data.Entity namespace.
                // This method enumerates the results of the query,
                // similar to ToList but without creating a list.
                // When used with Linq to Entities this method
                // creates entity objects and adds them to the context.
                _context.Categories.Load();

                // After the data is loaded call the DbSet<T>.Local property
                // to use the DbSet<T> as a binding source.
                categoryViewSource.Source = _context.Categories.Local;
            }

            private void buttonSave_Click(object sender, RoutedEventArgs e)
            {
                // When you delete an object from the related entities collection
                // (in this case Products), the Entity Framework doesn’t mark
                // these child entities as deleted.
                // Instead, it removes the relationship between the parent and the child
                // by setting the parent reference to null.
                // So we manually have to delete the products
                // that have a Category reference set to null.

                // The following code uses LINQ to Objects
                // against the Local collection of Products.
                // The ToList call is required because otherwise the collection will be modified
                // by the Remove call while it is being enumerated.
                // In most other situations you can use LINQ to Objects directly
                // against the Local property without using ToList first.
                foreach (var product in _context.Products.Local.ToList())
                {
                    if (product.Category == null)
                    {
                        _context.Products.Remove(product);
                    }
                }

                _context.SaveChanges();
                // Refresh the grids so the database generated values show up.
                this.categoryDataGrid.Items.Refresh();
                this.productsDataGrid.Items.Refresh();
            }

            protected override void OnClosing(System.ComponentModel.CancelEventArgs e)
            {
                base.OnClosing(e);
                this._context.Dispose();
            }
        }

    }
```

## <a name="test-the-wpf-application"></a>Testen der WPF-Anwendung

-   Kompilieren Sie die Anwendung, und führen Sie sie aus. Wenn Sie Code First verwendet haben, werden Sie feststellen, dass eine **wpfwitef Sample. productcontext** -Datenbank für Sie erstellt wird.
-   Geben Sie einen Kategorienamen im oberen Raster ein, und die Produktnamen im unteren Raster *geben in den ID-Spalten nichts ein, da der Primärschlüssel von der Datenbank generiert wird* .

    ![Hauptfenster mit neuen Kategorien und Produkten](~/ef6/media/screen1.png)

-   Klicken Sie auf die Schaltfläche **Speichern** , um die Daten in der Datenbank zu speichern

Nach dem Aufrufe von " **SaveChanges ()**" von dbcontext werden die IDs mit den von der Datenbank generierten Werten aufgefüllt. Da wir " **Refresh ()** " nach " **SaveChanges ()** " aufgerufen haben, werden die **DataGrid-** Steuerelemente ebenfalls mit den neuen Werten aktualisiert.

![Hauptfenster mit aufgefüllten IDs](~/ef6/media/screen2.png)

## <a name="additional-resources"></a>Weitere Ressourcen

Weitere Informationen zur Datenbindung an Auflistungen mithilfe von WPF finden Sie in [diesem Thema](https://docs.microsoft.com/dotnet/framework/wpf/data/data-binding-overview#binding-to-collections) in der WPF-Dokumentation.  
