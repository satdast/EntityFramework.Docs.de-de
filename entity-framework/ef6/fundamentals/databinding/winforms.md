---
title: Datenbindung mit WinForms-EF6
description: Datenbindung mit WinForms in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 80fc5062-2f1c-4dbd-ab6e-b99496784b36
uid: ef6/fundamentals/databinding/winforms
ms.openlocfilehash: 934ea2ae488a7df128746a0607037d85e9b23771
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616343"
---
# <a name="databinding-with-winforms"></a>Datenbindung mit Windows Forms
In dieser schrittweisen exemplarischen Vorgehensweise wird veranschaulicht, wie poco-Typen an Windows Forms-Steuerelemente (WinForms) in einem Master-Detail-Formular gebunden werden. Die Anwendung verwendet Entity Framework zum Auffüllen von Objekten mit Daten aus der Datenbank, zum Nachverfolgen von Änderungen und zum persistenten Speichern von Daten in der Datenbank.

Das Modell definiert zwei Typen, die an einer 1: n-Beziehung beteiligt sind: Kategorie (Prinzipal \\ Master) und Produkt (abhängige \\ Details). Anschließend werden die Visual Studio-Tools verwendet, um die im Modell definierten Typen an die WinForms-Steuerelemente zu binden. Das Daten Bindungs Framework von WinForms ermöglicht die Navigation zwischen verknüpften Objekten: Wenn Sie Zeilen in der Master Ansicht auswählen, wird die Detailansicht mit den entsprechenden untergeordneten Daten aktualisiert.

Die Screenshots und Code Auflistungen in dieser exemplarischen Vorgehensweise werden aus Visual Studio 2013 erstellt, aber Sie können diese exemplarische Vorgehensweise mit Visual Studio 2012 oder Visual Studio 2010 durcharbeiten.

## <a name="pre-requisites"></a>Voraussetzungen

Zum Durchführen dieser exemplarischen Vorgehensweise muss Visual Studio 2013, Visual Studio 2012 oder Visual Studio 2010 installiert sein.

Wenn Sie Visual Studio 2010 verwenden, müssen Sie auch nuget installieren. Weitere Informationen finden Sie unter [Installieren von nuget](https://docs.nuget.org/docs/start-here/installing-nuget).

## <a name="create-the-application"></a>Erstellen der Anwendung

-   Öffnen Sie Visual Studio.
-   **Datei- &gt; New- &gt; Project...**
-   Wählen Sie im linken Bereich **Fenster** und im rechten Bereich **Windows formsapplication** aus.
-   Geben Sie **winformswitef Sample** als Name ein.
-   Klicken Sie auf **OK**.

## <a name="install-the-entity-framework-nuget-package"></a>Installieren des Entity Framework nuget-Pakets

-   Klicken Sie in Projektmappen-Explorer mit der rechten Maustaste auf das **winformswideratsample** -Projekt.
-   Wählen Sie **nuget-Pakete verwalten... aus.**
-   Wählen Sie im Dialogfeld nuget-Pakete verwalten die Registerkarte **Online** aus, und wählen Sie das Paket **EntityFramework** aus.
-   Klicken Sie auf **Install** (Installieren).  
    > [!NOTE]
    > Zusätzlich zur EntityFramework-Assembly wird auch ein Verweis auf "System. ComponentModel. DataAnnotations" hinzugefügt. Wenn das Projekt einen Verweis auf System. Data. Entity enthält, wird es entfernt, wenn das EntityFramework-Paket installiert wird. Die System. Data. Entity-Assembly wird für Entity Framework 6-Anwendungen nicht mehr verwendet.

## <a name="implementing-ilistsource-for-collections"></a>Implementieren von IListSource für Sammlungen

Sammlungs Eigenschaften müssen die IListSource-Schnittstelle implementieren, um bei Verwendung von Windows Forms die bidirektionale Datenbindung mit Sortierung zu ermöglichen. Zu diesem Zweck wird ObservableCollection erweitert, um die IListSource-Funktionalität hinzuzufügen.

-   Fügen Sie dem Projekt eine **observablelistsource** -Klasse hinzu:
    -   Klicken Sie mit der rechten Maustaste auf den Projektnamen.
    -   Wählen Sie **Add- &gt; New Item** aus.
    -   Wählen Sie **Klasse** aus, und geben Sie **observablelistsource** als Klassennamen ein.
-   Ersetzen Sie den standardmäßig generierten Code durch den folgenden Code:

*Diese Klasse ermöglicht die bidirektionale Datenbindung und Sortierung. Die-Klasse wird von ObservableCollection &lt; T abgeleitet &gt; und fügt eine explizite Implementierung von IListSource hinzu. Die GetList ()-Methode von IListSource wird implementiert, um eine IBindingList-Implementierung zurückzugeben, die mit der ObservableCollection synchron bleibt. Die von "debindinglist" generierte IBindingList-Implementierung unterstützt die Sortierung. Die "tybindinglist"-Erweiterungsmethode wird in der EntityFramework-Assembly definiert.*

``` csharp
    using System.Collections;
    using System.Collections.Generic;
    using System.Collections.ObjectModel;
    using System.ComponentModel;
    using System.Diagnostics.CodeAnalysis;
    using System.Data.Entity;

    namespace WinFormswithEFSample
    {
        public class ObservableListSource<T> : ObservableCollection<T>, IListSource
            where T : class
        {
            private IBindingList _bindingList;

            bool IListSource.ContainsListCollection { get { return false; } }

            IList IListSource.GetList()
            {
                return _bindingList ?? (_bindingList = this.ToBindingList());
            }
        }
    }
```

## <a name="define-a-model"></a>Definieren eines Modells

In dieser exemplarischen Vorgehensweise können Sie ein Modell mithilfe von Code First oder dem EF-Designer implementieren. Führen Sie einen der beiden folgenden Abschnitte aus.

### <a name="option-1-define-a-model-using-code-first"></a>Option 1: Definieren eines Modells mit Code First

In diesem Abschnitt wird gezeigt, wie ein Modell und die zugehörige Datenbank mithilfe Code First erstellt werden. Fahren Sie mit dem nächsten Abschnitt fort (**Option 2: Definieren eines Modells mithilfe Database First)** , wenn Sie stattdessen Database First verwenden möchten, um das Modell aus einer Datenbank mit dem EF-Designer umzukehren.

Wenn Sie Code First Entwicklung verwenden, schreiben Sie in der Regel .NET Framework Klassen, die ihr konzeptionelles (Domänen-) Modell definieren.

-   Neue **Produkt** Klasse zu Projekt hinzufügen
-   Ersetzen Sie den standardmäßig generierten Code durch den folgenden Code:

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;

    namespace WinFormswithEFSample
    {
        public class Product
        {
            public int ProductId { get; set; }
            public string Name { get; set; }

            public int CategoryId { get; set; }
            public virtual Category Category { get; set; }
        }
    }
```

-   Fügen Sie dem Projekt eine **Category** -Klasse hinzu.
-   Ersetzen Sie den standardmäßig generierten Code durch den folgenden Code:

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;

    namespace WinFormswithEFSample
    {
        public class Category
        {
            private readonly ObservableListSource<Product> _products =
                    new ObservableListSource<Product>();

            public int CategoryId { get; set; }
            public string Name { get; set; }
            public virtual ObservableListSource<Product> Products { get { return _products; } }
        }
    }
```

Zusätzlich zum Definieren von Entitäten müssen Sie eine Klasse definieren, die von **dbcontext** abgeleitet ist und **dbset &lt; TEntity &gt; ** -Eigenschaften verfügbar macht. Die **dbset** -Eigenschaften lassen den Kontext erkennen, welche Typen Sie in das Modell einschließen möchten. Die Typen " **dbcontext** " und " **dbset** " werden in der EntityFramework-Assembly definiert.

Eine Instanz des von DbContext abgeleiteten Typs verwaltet die Entitätsobjekte während der Laufzeit, was das Auffüllen der Objekte mit Daten aus einer Datenbank, die Änderungsnachverfolgung und das persistente Speichern von Daten in der Datenbank umfasst.

-   Fügen Sie dem Projekt eine neue **productcontext** -Klasse hinzu.
-   Ersetzen Sie den standardmäßig generierten Code durch den folgenden Code:

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Data.Entity;
    using System.Linq;
    using System.Text;

    namespace WinFormswithEFSample
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

Sobald der Reverse-Engineering-Prozess abgeschlossen ist, wird das neue Modell dem Projekt hinzugefügt und geöffnet, damit Sie es im Entity Framework Designer anzeigen können. Außerdem wurde dem Projekt eine App.config Datei mit den Verbindungsdetails für die Datenbank hinzugefügt.

#### <a name="additional-steps-in-visual-studio-2010"></a>Weitere Schritte in Visual Studio 2010

Wenn Sie in Visual Studio 2010 arbeiten, müssen Sie den EF-Designer aktualisieren, damit die EF6-Codegenerierung verwendet werden kann.

-   Klicken Sie mit der rechten Maustaste auf eine leere Stelle des Modells im EF-Designer, und wählen Sie **Code Generierungs Element hinzufügen... aus.**
-   Wählen Sie im linken Menü **Online Vorlagen** aus, und suchen Sie nach **dbcontext** .
-   Wählen Sie den **EF 6. x dbcontext Generator für C aus \# ,** geben Sie **ProductModel** als Name ein, und klicken Sie auf hinzufügen.

#### <a name="updating-code-generation-for-data-binding"></a>Aktualisieren der Codegenerierung für die Datenbindung

EF generiert Code aus dem Modell mithilfe von T4-Vorlagen. Die Vorlagen, die in Visual Studio enthalten sind oder aus der Visual Studio Gallery heruntergeladen wurden, sind für die allgemeine Verwendung vorgesehen. Dies bedeutet, dass die von diesen Vorlagen generierten Entitäten einfache ICollection &lt; T- &gt; Eigenschaften haben. Bei der Datenbindung ist es jedoch wünschenswert, dass Sammlungs Eigenschaften vorhanden sind, die IListSource implementieren. Aus diesem Grund haben wir die oben genannte observablelistsource-Klasse erstellt und nun die Vorlagen so ändern, dass diese Klasse verwendet wird.

-   Öffnen Sie die **Projektmappen-Explorer** , und suchen Sie nach der Datei **ProductModel. edmx.**
-   Suchen Sie die **ProductModel.tt** -Datei, die unter der Datei "ProductModel. edmx" eingefügt wird.

    ![Produktmodell Vorlage](~/ef6/media/productmodeltemplate.png)

-   Doppelklicken Sie auf die Datei ProductModel.tt, um Sie im Visual Studio-Editor zu öffnen.
-   Suchen und ersetzen Sie die beiden Vorkommen von "**ICollection**" durch "**observablelistsource**". Diese befinden sich in ungefähr den Zeilen 296 und 484.
-   Suchen und ersetzen Sie das erste Vorkommen von "**HashSet**" durch "**observablelistsource**". Dieser Vorgang befindet sich in ungefähr Zeile 50. Ersetzen **Sie nicht** das zweite Vorkommen des Hashsets, das später im Code gefunden wurde.
-   Speichern Sie die Datei ProductModel.tt. Dies sollte bewirken, dass der Code für Entitäten erneut generiert wird. Wenn der Code nicht automatisch neu generiert wird, klicken Sie mit der rechten Maustaste auf ProductModel.tt, und wählen Sie "benutzerdefiniertes Tool ausführen".

Wenn Sie nun die Category.cs-Datei öffnen (die unter ProductModel.tt angezeigt wird), sollten Sie sehen, dass die Products-Sammlung den Typ **observablelistsource &lt; &gt; **aufweist.

Kompilieren Sie das Projekt.

## <a name="lazy-loading"></a>Lazy Loading

Die **Products**-Eigenschaft für die **Category**-Klasse und die **Category**-Eigenschaft für die **Product**-Klasse sind Navigationseigenschaften. Im Entity Framework bieten Navigationseigenschaften eine Möglichkeit, in einer Beziehung zwischen zwei Entitätstypen zu navigieren.

EF bietet Ihnen die Möglichkeit, Verwandte Entitäten aus der Datenbank automatisch zu laden, wenn Sie zum ersten Mal auf die Navigations Eigenschaft zugreifen. Beachten Sie bei dieser Art von Laden (das als „Lazy Loading“ bezeichnet wird), dass beim ersten Zugriff auf jede Navigationseigenschaft eine separate Abfrage für die Datenbank ausgeführt wird, wenn sich die Inhalte nicht bereits im Kontext befinden.

Wenn poco-Entitäts Typen verwendet werden, erreicht EF Lazy Loading durch Erstellen von Instanzen abgeleiteter Proxy Typen während der Laufzeit und das anschließende Überschreiben virtueller Eigenschaften in den Klassen, um den Lade Hook hinzuzufügen. Wenn Sie Lazy Loading verknüpfter Objekte abrufen möchten, müssen Sie die Navigations Eigenschaften Getter als **Public** und **Virtual** (**Overridable** in Visual Basic) deklarieren, und die Klasse darf nicht **versiegelt** sein (**nodeverridable** in Visual Basic). Bei Verwendung Database First Navigations Eigenschaften automatisch als virtuell fest, um Lazy Loading zu aktivieren. Im Code First Abschnitt haben wir entschieden, die Navigations Eigenschaften aus demselben Grund virtuell zu machen.

## <a name="bind-object-to-controls"></a>Binden von Objekten an Steuerelemente

Fügen Sie die Klassen, die im Modell definiert sind, als Datenquellen für diese WinForms-Anwendung hinzu.

-   Wählen Sie im Hauptmenü **Projekt- &gt; neue Datenquelle hinzufügen... aus.**
    (in Visual Studio 2010 müssen Sie **Daten- &gt; neue Datenquelle hinzufügen...**)
-   Wählen Sie im Fenster Daten Quellentyp auswählen die Option **Objekt** aus, und klicken Sie auf **weiter** .
-   Erweitern Sie im Dialogfeld Wählen Sie die Datenobjekte aus das **winformswiderfsample** -Objekt zweimal, und wählen Sie die **Kategorie** aus. es ist nicht erforderlich, die Produktdaten Quelle auszuwählen, da wir Sie über die-Eigenschaft des Produkts in der Kategorie Datenquelle erhalten.

    ![Datenquelle](~/ef6/media/datasource.png)

-   Klicken Sie auf **Fertig stellen**.
    Wenn das Fenster Datenquellen nicht angezeigt wird, wählen Sie **Ansicht- &gt; Weitere Windows- &gt; Datenquellen** aus.
-   Klicken Sie auf das anheft Symbol, damit das Fenster Datenquellen nicht automatisch ausgeblendet wird. Sie müssen möglicherweise auf die Schaltfläche "Aktualisieren" klicken, wenn das Fenster bereits sichtbar war.

    ![Datenquelle 2](~/ef6/media/datasource2.png)

-   Doppelklicken Sie in Projektmappen-Explorer auf die Datei **Form1.cs** , um das Hauptformular im-Designer zu öffnen.
-   Wählen Sie die **kategoriedatenquelle** aus, und ziehen Sie Sie auf das Formular. Standardmäßig werden dem Designer neue DataGridView-Steuerelemente (**categorydatagridview**) und Navigations Symbolleisten-Steuerelemente hinzugefügt. Diese Steuerelemente sind an die Komponenten BindingSource (**categorybindingsource**) und Binding Navigator (**categorybindingnavigator**) gebunden, die ebenfalls erstellt werden.
-   Bearbeiten Sie die Spalten in der **categorydatagridview**. Die **CategoryID** -Spalte soll als schreibgeschützt festgelegt werden. Der Wert für die **CategoryID** -Eigenschaft wird von der Datenbank generiert, nachdem die Daten gespeichert wurden.
    -   Klicken Sie mit der rechten Maustaste auf das DataGridView-Steuerelement, und wählen Sie Spalten bearbeiten aus.
    -   Wählen Sie die Spalte CategoryID aus, und legen Sie schreibgeschützt auf true fest.
    -   OK drücken
-   Wählen Sie unter der Kategorie Datenquelle die Option Produkte aus, und ziehen Sie Sie auf das Formular. Productdatagridview und ProductBindingSource werden dem Formular hinzugefügt.
-   Bearbeiten Sie die Spalten in productdatagridview. Wir möchten die Spalten "CategoryID" und "Category" ausblenden und "ProductID" auf "schreibgeschützt" festlegen. Der Wert für die ProductID-Eigenschaft wird von der Datenbank generiert, nachdem die Daten gespeichert wurden.
    -   Klicken Sie mit der rechten Maustaste auf das DataGridView-Steuerelement, und wählen Sie **Spalten bearbeiten**aus.
    -   Wählen Sie die Spalte **ProductID** aus **, und legen Sie Schreib** geschützt auf **true**fest.
    -   Wählen Sie die Spalte **CategoryID** aus, und klicken Sie auf die Schaltfläche **Entfernen** . Gehen Sie mit der Spalte **Category** gleich vor.
    -   Klicken Sie auf **OK**.

    Bisher haben wir unsere DataGridView-Steuerelemente mit BindingSource-Komponenten im Designer verknüpft. Im nächsten Abschnitt wird dem Code Behind Code hinzugefügt, um categorybindingsource. DataSource auf die Auflistung von Entitäten festzulegen, die derzeit von dbcontext nachverfolgt werden. Wenn wir die Produkte aus der Kategorie gezogen und abgelegt haben, haben die WinForms das Einrichten der ProductBindingSource. DataSource-Eigenschaft in der categorybindingsource-Eigenschaft und der ProductBindingSource. DataMember-Eigenschaft in Produkte erledigt. Aufgrund dieser Bindung werden nur die Produkte, die der aktuell ausgewählten Kategorie angehören, in der productdatagridview angezeigt.
-   Aktivieren Sie die Schaltfläche **Speichern** auf der Navigations Symbolleiste, indem Sie auf die Rechte Maustaste klicken und **aktivieren**auswählen.

    ![Designer von Formular 1](~/ef6/media/form1-designer.png)

-   Fügen Sie den Ereignishandler für die Schaltfläche Speichern hinzu, indem Sie auf die Schaltfläche doppelklicken. Dadurch wird der Ereignishandler hinzugefügt, und Sie gelangen zum Code Behind für das Formular. Der Code für den " **categorybindingnavigatorsaveitem"- \_ Click** -Ereignishandler wird im nächsten Abschnitt hinzugefügt.

## <a name="add-the-code-that-handles-data-interaction"></a>Fügen Sie den Code hinzu, der die Daten Interaktion behandelt.

Nun fügen wir den Code hinzu, um den productcontext zum Ausführen von Datenzugriff zu verwenden. Aktualisieren Sie den Code für das Hauptformular Fenster, wie unten gezeigt.

Der Code deklariert eine Instanz von productcontext mit langer Laufzeit. Das productcontext-Objekt wird verwendet, um Daten abzufragen und in der Datenbank zu speichern. Die verwerfen ()-Methode der productcontext-Instanz wird dann von der überschriebenen OnClosing-Methode aufgerufen. Die Code Kommentare enthalten Details zum Funktionsumfang des Codes.

``` csharp
    using System;
    using System.Collections.Generic;
    using System.ComponentModel;
    using System.Data;
    using System.Drawing;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.Windows.Forms;
    using System.Data.Entity;

    namespace WinFormswithEFSample
    {
        public partial class Form1 : Form
        {
            ProductContext _context;
            public Form1()
            {
                InitializeComponent();
            }

            protected override void OnLoad(EventArgs e)
            {
                base.OnLoad(e);
                _context = new ProductContext();

                // Call the Load method to get the data for the given DbSet
                // from the database.
                // The data is materialized as entities. The entities are managed by
                // the DbContext instance.
                _context.Categories.Load();

                // Bind the categoryBindingSource.DataSource to
                // all the Unchanged, Modified and Added Category objects that
                // are currently tracked by the DbContext.
                // Note that we need to call ToBindingList() on the
                // ObservableCollection<TEntity> returned by
                // the DbSet.Local property to get the BindingList<T>
                // in order to facilitate two-way binding in WinForms.
                this.categoryBindingSource.DataSource =
                    _context.Categories.Local.ToBindingList();
            }

            private void categoryBindingNavigatorSaveItem_Click(object sender, EventArgs e)
            {
                this.Validate();

                // Currently, the Entity Framework doesn’t mark the entities
                // that are removed from a navigation property (in our example the Products)
                // as deleted in the context.
                // The following code uses LINQ to Objects against the Local collection
                // to find all products and marks any that do not have
                // a Category reference as deleted.
                // The ToList call is required because otherwise
                // the collection will be modified
                // by the Remove call while it is being enumerated.
                // In most other situations you can do LINQ to Objects directly
                // against the Local property without using ToList first.
                foreach (var product in _context.Products.Local.ToList())
                {
                    if (product.Category == null)
                    {
                        _context.Products.Remove(product);
                    }
                }

                // Save the changes to the database.
                this._context.SaveChanges();

                // Refresh the controls to show the values         
                // that were generated by the database.
                this.categoryDataGridView.Refresh();
                this.productsDataGridView.Refresh();
            }

            protected override void OnClosing(CancelEventArgs e)
            {
                base.OnClosing(e);
                this._context.Dispose();
            }
        }
    }
```

## <a name="test-the-windows-forms-application"></a>Testen der Windows Forms Anwendung

-   Kompilieren Sie die Anwendung, und führen Sie Sie aus, und Sie können die Funktionalität testen.

    ![Formular 1 vor dem Speichern](~/ef6/media/form1beforesave.png)

-   Nach dem Speichern der Speicher generierten Schlüssel werden auf dem Bildschirm angezeigt.

    ![Formular 1 nach dem Speichern](~/ef6/media/form1aftersave.png)

-   Wenn Sie Code First verwendet haben, sehen Sie auch, dass eine **winformswitef Sample. productcontext** -Datenbank für Sie erstellt wird.

    ![Server Objekt-Explorer](~/ef6/media/serverobjexplorer.png)
