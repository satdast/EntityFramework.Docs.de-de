---
title: 'Erste Schritte mit WPF: EF Core'
description: Tutorial zu den ersten Schritten für die Verwendung von WPF mit Entity Framework Core
author: jeremylikness
ms.author: jeliknes
ms.date: 07/24/2020
uid: core/get-started/wpf
ms.openlocfilehash: f183064fafbe2d0e7b8dbdafa921169afc9ffe78
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429922"
---
# <a name="getting-started-with-wpf"></a>Erste Schritte mit WPF

In dieser schrittweisen exemplarischen Vorgehensweise wird gezeigt, wie POCO-Typen an WPF-Steuerelemente in einem „main-detail“-Formular gebunden werden. Die Anwendung verwendet die Entity Framework-APIs zum Auffüllen von Objekten mit Daten aus der Datenbank, zum Nachverfolgen von Änderungen und zum persistenten Speichern von Daten in der Datenbank.

Das Modell definiert zwei Typen, die eine 1:n-Beziehung eingehen: **Category** (principal\\main) und **Product** (dependent\\detail). Das WPF-Datenbindungsframework ermöglicht die Navigation zwischen verbundenen Objekten: durch Auswählen von Zeilen in der Masteransicht wird die Detailansicht mit den entsprechenden untergeordneten Daten aktualisiert.

Die Screenshots und Codeauflistungen in dieser exemplarischen Vorgehensweise stammen aus Visual Studio 2019 16.6.5.

> [!TIP]
> Das in diesem Artikel verwendete [Beispiel finden Sie auf GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/WPF).

## <a name="pre-requisites"></a>Voraussetzungen

Um diese exemplarische Vorgehensweise nachvollziehen zu können, müssen Sie Visual Studio 2019 16.3 oder höher mit ausgewählter **.NET-Desktopworkload** installiert haben. Weitere Informationen zur Installation der neuesten Version von Visual Studio finden Sie unter [Installieren von Visual Studio](/visualstudio/install/install-visual-studio).

## <a name="create-the-application"></a>Erstellen der Anwendung

1. Öffnen Sie Visual Studio.
2. Wählen Sie im Startfenster **Neues Projekt erstellen** aus.
3. Suchen Sie nach „WPF“, wählen Sie **WPF-App (.NET Core)** aus, und wählen Sie dann **Weiter** aus.
4. Geben Sie dem Projekt auf dem nächsten Bildschirm einen Namen (z. B. **GetStartedWPF** ), und wählen Sie **Erstellen** aus.

## <a name="install-the-entity-framework-nuget-packages"></a>Installieren des Entity Framework-NuGet-Pakets

1. Klicken Sie mit der rechten Maustaste auf die Projektmappe, und wählen Sie **NuGet-Pakete für Projektmappe verwalten** aus.

    ![NuGet-Pakete verwalten](_static/wpf-tutorial-nuget.jpg)

1. Geben Sie im Suchfeld als Suchbegriff `entityframeworkcore.sqlite` ein.
1. Wählen Sie das Paket **Microsoft.EntityFrameworkCore.Sqlite** aus.
1. Wählen Sie das Projekt im rechten Bereich aus, und klicken Sie auf **Installieren**.

    ![SQLite-Paket](_static/wpf-tutorial-sqlite.jpg)

1. Wiederholen Sie die Schritte, um nach `entityframeworkcore.proxies` zu suchen und **Microsoft.EntityFrameworkCore.Proxies** zu installieren.

> [!NOTE]
> Als Sie das SQLite-Paket installiert haben, wurde das zugehörige **Microsoft.EntityFrameworkCore** -Basispaket automatisch heruntergeladen. Das **Microsoft. EntityFrameworkCore.Proxies** -Paket unterstützt „Lazy Loading“ von Daten. Dies bedeutet, dass bei Entitäten mit untergeordneten Entitäten nur die übergeordneten Elemente beim anfänglichen Laden abgerufen werden. Die Proxys erkennen, wenn versucht wird, auf die untergeordneten Entitäten zuzugreifen. Diese werden bei Bedarf automatisch geladen.

## <a name="define-a-model"></a>Definieren eines Modells

In dieser exemplarischen Vorgehensweise implementieren Sie ein Modell mit „Code First“. Dies bedeutet, dass EF Core die Datenbanktabellen und das Schema basierend auf den von Ihnen definierten C#-Klassen erstellt.

Fügen Sie eine neue Klasse hinzu. Geben Sie ihr den Namen `Product.cs`, und füllen Sie sie wie folgt mit Daten auf:

**`Product.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/Product.cs)]

Fügen Sie dann eine Klasse mit dem Namen `Category.cs` hinzu, und füllen Sie sie mit folgendem Code auf:

**`Category.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/Category.cs)]

Die **Products** -Eigenschaft für die **Category** -Klasse und die **Category** -Eigenschaft für die **Product** -Klasse sind Navigationseigenschaften. Im Entity Framework bieten Navigationseigenschaften eine Möglichkeit, in einer Beziehung zwischen zwei Entitätstypen zu navigieren.

Zusätzlich zur Definition von Entitäten müssen Sie eine Klasse definieren, die von DbContext abgeleitet ist und DbSet&lt;TEntity&gt;-Eigenschaften bereitstellt. Die DbSet&lt;TEntity&gt;-Eigenschaften informieren den Kontext darüber, welche Typen Sie in das Modell einbeziehen möchten.

Eine Instanz des von DbContext abgeleiteten Typs verwaltet die Entitätsobjekte während der Laufzeit, was das Auffüllen der Objekte mit Daten aus einer Datenbank, die Änderungsnachverfolgung und das persistente Speichern von Daten in der Datenbank umfasst.

Fügen Sie dem Projekt eine neue `ProductContext.cs`-Klasse mit der folgenden Definition hinzu:

**`ProductContext.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/ProductContext.cs)]

* `DbSet` informiert EF Core, welche C#-Entitäten der Datenbank zugeordnet werden sollen.
* Es gibt eine Vielzahl von Möglichkeiten, `DbContext` von EF Core zu konfigurieren. Informationen zu diesem Thema finden Sie hier: [Konfigurieren eines DbContext](xref:core/dbcontext-configuration/index).
* In diesem Beispiel wird die `OnConfiguring`-Überschreibung verwendet, um eine SQLite-Datendatei anzugeben.
* Der `UseLazyLoadingProxies`-Aufruf informiert EF Core, Lazy Loading zu implementieren, damit untergeordnete Entitäten automatisch geladen werden, wenn der Zugriff über das übergeordnete Element erfolgt.

Drücken Sie **STRG+UMSCHALT+B** , oder navigieren Sie zu **Build &gt; Projektmappe erstellen** , um das Projekt zu kompilieren.

> [!TIP]
> Informieren Sie sich über die verschiedenen Möglichkeiten, die Datenbank und EF Core-Modelle synchron zu halten: [Verwalten von Datenbankschemas](xref:core/managing-schemas/index).

## <a name="lazy-loading"></a>Lazy Loading

Die **Products** -Eigenschaft für die **Category** -Klasse und die **Category** -Eigenschaft für die **Product** -Klasse sind Navigationseigenschaften. In Entity Framework Core bieten Navigationseigenschaften eine Möglichkeit, in einer Beziehung zwischen zwei Entitätstypen zu navigieren.

EF Core bietet Ihnen die Möglichkeit, verwandte Entitäten aus der Datenbank automatisch zu laden, wenn Sie zum ersten Mal auf die Navigationseigenschaft zugreifen. Beachten Sie bei dieser Art von Laden (das als „Lazy Loading“ bezeichnet wird), dass beim ersten Zugriff auf jede Navigationseigenschaft eine separate Abfrage für die Datenbank ausgeführt wird, wenn sich die Inhalte nicht bereits im Kontext befinden.

Bei der Verwendung von POCO-Entitätstypen („Plain Old C# Object“) erreicht EF Core Lazy Loading, indem während der Laufzeit Instanzen abgeleiteter Proxytypen erstellt und dann virtuelle Eigenschaften in Ihren Klassen überschrieben werden, um den Ladehook hinzuzufügen. Um Lazy Loading von verwandten Objekten zu erzielen, müssen Sie Navigationseigenschaftsgetter als **public** und **virtual** deklarieren ( **Overridable** in Visual Basic), und Ihre Klasse darf nicht **sealed** ( **NotOverridable** in Visual Basic) sein. Wenn Database First verwendet wird, werden Navigationseigenschaften automatisch als virtuell festgelegt, um Lazy Loading zu ermöglichen.

## <a name="bind-object-to-controls"></a>Binden von Objekten an Steuerelemente

Fügen Sie die Klassen, die im Modell definiert sind, als Datenquellen für diese WPF-Anwendung hinzu.

1. Doppelklicken Sie im Projektmappen-Explorer auf **MainWindow.xaml** , um das Hauptformular zu öffnen.
1. Wählen Sie die Registerkarte **XAML** aus, um den XAML-Code zu bearbeiten.
1. Fügen Sie direkt nach dem öffnenden `Window`-Tag die folgenden Quellen hinzu, um eine Verbindung mit den EF Core-Entitäten herzustellen.

    [!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=1-13&highlight=9-13)]

1. Dies legt die Quelle für die „übergeordneten“ Kategorien und die zweite Quelle für die „detail“-Produkte fest.
1. Fügen Sie dem XAML dann das folgende Markup nach dem schließenden `Window.Resources`-Tag hinzu.

    [!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=15-26)]

1. Beachten Sie, dass `CategoryId` auf `ReadOnly` festgelegt ist, da die ID von der Datenbank zugewiesen wird und nicht geändert werden kann.

## <a name="adding-a-details-grid"></a>Hinzufügen eines Detailrasters

Da das Raster nun zum Anzeigen von Kategorien vorhanden ist, kann das Detailraster zum Anzeigen von Produkten hinzugefügt werden.

**`MainWindow.xaml`**

[!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=27-40)]

Fügen Sie schließlich eine Schaltfläche `Save` hinzu, und verbinden Sie das click-Ereignis mit `Button_Click`.

[!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=41-42)]

Die Entwurfsansicht sollte wie folgt aussehen:

![Screenshot des WPF-Designers](_static/wpf-tutorial-designer.jpg)

## <a name="add-code-that-handles-data-interaction"></a>Hinzufügen von Code, der die Dateninteraktion verarbeitet

Es ist an der Zeit, dem Hauptfenster einige Ereignishandler hinzuzufügen.

1. Klicken Sie im XAML-Fenster auf das Fenster **&lt;Window&gt;** -Element, um das Hauptfenster auszuwählen.
1. Wählen Sie im Fenster **Eigenschaften** oben rechts **Ereignisse** aus, und doppelklicken Sie dann auf das Textfeld rechts neben der Bezeichnung **Geladen**.

    ![Eigenschaften des Hauptfensters](_static/wpf-tutorial-loaded.jpg)

Damit gelangen Sie zum Code Behind für das Formular. Nun bearbeiten wir den Code so, dass `ProductContext` für den Datenzugriff verwendet wird. Aktualisieren Sie den Code wie unten gezeigt.

Der Code deklariert eine Instanz von `ProductContext` mit langer Ausführungszeit. Das `ProductContext`-Objekt wird verwendet, um Daten abzufragen und in der Datenbank zu speichern. Die `Dispose()`-Methode für die `ProductContext`-Instanz wird dann von der überschriebenen `OnClosing`-Methode aufgerufen. Die Codekommentare erläutern die Funktionsweise der einzelnen Schritte.

**`MainWindow.xaml.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml.cs)]

> [!NOTE]
> Der Code verwendet einen Aufruf von `EnsureCreated()` zum Erstellen der Datenbank bei der ersten Ausführung. Dies ist für Demos akzeptabel, aber in Produktions-Apps sollten Sie [Migrationen](xref:core/managing-schemas/migrations/index) verwenden, um Ihr Schema zu verwalten. Der Code wird auch synchron ausgeführt, da er eine lokale SQLite-Datenbank verwendet. In Produktionsszenarien, in denen normalerweise ein Remoteserver verwendet wird, sollten Sie die Verwendung der asynchronen Versionen der Methoden `Load` und `SaveChanges` in Erwägung ziehen.

## <a name="test-the-wpf-application"></a>Testen der WPF-Anwendung

Kompilieren Sie die Anwendung, und führen Sie sie aus, indem Sie **F5** drücken oder **Debuggen &gt; Debuggen starten** auswählen. Die Datenbank sollte automatisch mit einer Datei namens `products.db` erstellt werden. Geben Sie einen Kategorienamen ein, und drücken Sie die EINGABETASTE. Fügen Sie dann dem unteren Raster Produkte hinzu. Klicken Sie auf „Speichern“, und beobachten Sie, wie das Raster mit den von der Datenbank bereitgestellten IDs aktualisiert wird. Markieren Sie eine Zeile, und klicken Sie auf **Löschen** , um die Zeile zu entfernen. Die Entität wird gelöscht, wenn Sie auf **Speichern** klicken.

![Ausgeführte Anwendung](_static/wpf-tutorial-app.jpg)

## <a name="property-change-notification"></a>Benachrichtigung der Eigenschaftenänderung

Dieses Beispiel basiert auf vier Schritten, um die Entitäten mit der Benutzeroberfläche zu synchronisieren.

1. Der anfängliche Aufruf von `_context.Categories.Load()` lädt die Kategoriedaten.
1. Die Lazy Loading-Proxys laden die Daten der abhängigen Produkte.
1. Die integrierte Änderungsnachverfolgung von EF Core nimmt die erforderlichen Änderungen an Entitäten vor, einschließlich Einfügungen und Löschungen, wenn `_context.SaveChanges()` aufgerufen wird.
1. Die Aufrufe von `DataGridView.Items.Refresh()` erzwingen einen erneuten Ladevorgang mit den neu generierten IDs.

Dies funktioniert für unser Beispiel mit ersten Schritten, aber Sie benötigen möglicherweise zusätzlichen Code für andere Szenarien. WPF-Steuerelemente rendern die Benutzeroberfläche, indem sie die Felder und Eigenschaften für Ihre Entitäten lesen. Wenn Sie einen Wert in der Benutzeroberfläche (User Interface, UI) bearbeiten, wird dieser Wert an Ihre Entität übergeben. Wenn Sie den Wert einer Eigenschaft direkt für Ihre Entität ändern, z. B. das Laden aus der Datenbank, spiegelt WPF die Änderungen nicht sofort in der Benutzeroberfläche wider. Die Rendering-Engine muss über die Änderungen benachrichtigt werden. Das Projekt hat dies durch manuelles Aufrufen von `Refresh()` erreicht. Eine einfache Möglichkeit, diese Benachrichtigung zu automatisieren, besteht darin, die [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged)-Schnittstelle zu implementieren. WPF-Komponenten erkennen die Schnittstelle automatisch und registrieren sich für Änderungsereignisse. Die Entität ist für das Auslösen dieser Ereignisse verantwortlich.

> [!TIP]
> Weitere Informationen zum Verarbeiten von Änderungen finden Sie unter: [Implementieren von Benachrichtigungen bei Eigenschaftenänderungen](/dotnet/framework/wpf/data/how-to-implement-property-change-notification).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum [Konfigurieren eines DbContext](xref:core/dbcontext-configuration/index).
