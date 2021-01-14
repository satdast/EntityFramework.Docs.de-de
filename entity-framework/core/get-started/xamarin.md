---
title: Erste Schritte mit EF Core und Xamarin – EF Core
description: Tutorial zu den ersten Schritten für die Verwendung von Xamarin zum Erstellen mobiler Apps mit Entity Framework Core
author: codemillmatt
ms.date: 07/07/2020
ms.author: masoucou
uid: core/get-started/xamarin
ms.openlocfilehash: c19c70903926c2c8ff4b51cca7781859af7e3567
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128783"
---
# <a name="getting-started-with-ef-core-and-xamarin"></a>Erste Schritte mit EF Core und Xamarin

In diesem Tutorial erstellen Sie eine [Xamarin.Forms](/xamarin/get-started/what-is-xamarin-forms)-Anwendung, die den Datenzugriff auf eine SQLite-Datenbank mithilfe von Entity Framework Core durchführt.

Sie können dieses Tutorial mit Visual Studio unter Windows oder Visual Studio für Mac abschließen.

> [!TIP]
> Das in diesem Artikel verwendete [Beispiel finden Sie auf GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Xamarin).

## <a name="prerequisites"></a>Voraussetzungen

Installieren Sie eines der folgenden Tools:

* [Visual Studio 2019 Version 16.3 oder höher ](https://www.visualstudio.com/downloads/) mit dieser Workload:
  * **Mobile Entwicklung mit .NET**
* [Visual Studio für Mac](https://visualstudio.microsoft.com/vs/mac/)

In dieser [Dokumentation finden Sie ausführliche Installationsanweisungen](/xamarin/get-started/installation) für jede Plattform.

## <a name="download-and-run-the-sample-project"></a>Herunterladen und Ausführen des Beispielprojekts

Laden Sie den Code von GitHub herunter, um diese Beispielanwendung auszuführen und zu untersuchen.

Sobald Sie sie heruntergeladen haben, öffnen Sie die Projektmappendatei `EFGettingStarted.sln` in Visual Studio oder Visual Studio für Mac, und führen Sie die Anwendung auf der Plattform Ihrer Wahl aus.

Beim ersten Start der App wird die lokale SQLite-Datenbank mit zwei Einträgen aufgefüllt, die Blogs darstellen.

![Screenshot: Seite mit der Liste aller Blogs](_static/xamarin-tutorial-1.png)

Klicken Sie in der Symbolleiste auf **Hinzufügen**.

Daraufhin wird eine neue Seite angezeigt, auf der Sie Informationen über einen neuen Blog eingeben können.

![Screenshot: Seite für neuen Blogeintrag](_static/xamarin-tutorial-2.png)

Geben Sie alle Informationen ein, und klicken Sie in der Symbolleiste auf **Speichern**. Der neue Blog wird in der SQLite-Datenbank der App gespeichert und in der Liste angezeigt.

Sie können auf einen der Blogeinträge in der Liste klicken, um alle Beiträge für diesen Blog anzuzeigen.

![Screenshot: Seite mit der Liste der Blogbeiträge](_static/xamarin-tutorial-3.png)

Klicken Sie auf der Symbolleiste auf **Hinzufügen**.

Dann wird eine Seite angezeigt, auf der Sie Informationen über einen neuen Blogbeitrag eingeben können.

![Screenshot: Seite zum Hinzufügen eines neuen Blogbeitrags](_static/xamarin-tutorial-4.png)

Geben Sie alle Informationen ein, und klicken Sie in der Symbolleiste auf **Speichern**.

Der neue Beitrag wird dem Blogbeitrag zugeordnet, auf den Sie im vorherigen Schritt geklickt haben, und dann in der SQLite-Datenbank der App gespeichert und in der Liste angezeigt.

Wechseln Sie zurück zur Seite mit der Liste der Blogs. Klicken Sie dann auf der Symbolleiste auf **Alle löschen**. Daraufhin werden alle Blogs und ihre zugehörigen Beiträge aus der SQLite-Datenbank der App gelöscht.

![Screenshot der App mit allen Blogs gelöscht](_static/xamarin-tutorial-5.png)

## <a name="explore-the-code"></a>Untersuchen des Codes

In den folgenden Abschnitten durchlaufen Sie den Code im Beispielprojekt, der Daten aus einer SQLite-Datenbank mithilfe von EF Core mit Xamarin.Forms liest, erstellt, aktualisiert und löscht.

Dabei wird davon ausgegangen, dass Sie mit den Xamarin.Forms-Themen zum [Anzeigen von Daten](/xamarin/xamarin-forms/app-fundamentals/data-binding/) und zum [Navigieren zwischen Seiten](/xamarin/xamarin-forms/app-fundamentals/navigation/) vertraut sind.

> [!IMPORTANT]
> Entity Framework Core verwendet Reflektion, um Funktionen aufzurufen, die der Xamarin.iOS-Linker in **Release**-Moduskonfigurationen möglicherweise entfernen könnte. Sie können eine von zwei Möglichkeiten auswählen, dies zu vermeiden.
>
> * Die erste ist, `--linkskip System.Core` zu **Weitere mtouch-Argumente** in den **iOS-Build**-Optionen hinzuzufügen.
> * Alternativ können Sie das Xamarin.iOS-**Linkerverhalten** in den **iOS-Build**-Optionen auf `Don't Link` festlegen.
> [Dieser Artikel enthält weitere Informationen zum Xamarin.iOS-Linker](/xamarin/ios/deploy-test/linker), unter anderem zum Festlegen des Verhaltens in Xamarin.iOS.
>

## <a name="entity-framework-core-nuget-packages"></a>NuGet-Pakete für Entity Framework Core

Zum Erstellen von Xamarin.Forms-Apps mit EF Core installieren Sie das Paket für den EF Core-Datenbankanbieter, den Sie als Ziel für alle Projekte in der Xamarin.Forms-Projektmappe verwenden möchten. In diesem Tutorial wird der SQLite-Anbieter verwendet.

Das folgende NuGet-Paket ist für alle Projekte in der Xamarin.Forms-Projektmappe erforderlich.

* `Microsoft.EntityFrameworkCore.Sqlite`

## <a name="model-classes"></a>Modellklassen

Alle Tabellen in der SQLite-Datenbank, auf die über EF Core zugegriffen wird, werden in einer Klasse modelliert. In diesem Beispiel werden zwei Klassen verwendet: `Blog` und `Post`, die sich im Ordner `Models` befinden.

Die Modellklassen bestehen lediglich aus Eigenschaften, die Spalten in der Datenbank modellieren.

* **Blog.cs**

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Models/Blog.cs)]

* Die Eigenschaft `Posts` definiert eine Über-/Unterordnungsbeziehung zwischen `Blog` und `Post`.

* **Post.cs**

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Models/Post.cs)]

* Die Eigenschaften `BlogId` und `Blog` beziehen sich auf das übergeordnete `Blog`-Objekt für die `Post`-Instanz.

## <a name="data-context"></a>Datenkontext

Die `BloggingContext`-Klasse befindet sich im Ordner `Services` und erbt von der EF Core-Klasse `DbContext`. Eine `DbContext`-Klasse wird zum Gruppieren von Datenbankabfragen und -änderungen verwendet.

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Services/BloggingContext.cs)]

* Beide Eigenschaften in dieser Klasse vom Typ `DbSet` werden verwendet, um die zugrunde liegenden Tabellen zu betreiben, die Blogs und Beiträge darstellen.
* `SQLitePCL.Batteries_V2.Init()` ist im Konstruktor erforderlich, um SQLite unter iOS zu initiieren.
* Die `OnConfiguring`-Funktion richtet den Speicherort der SQLite-Datenbank auf dem physischen Gerät ein.

## <a name="create-read-update--delete"></a>Erstellen, Lesen, Aktualisieren und Löschen

Im Folgenden werden einige Instanzen in der App aufgeführt, in der EF Core für den Zugriff auf SQLite verwendet wird.

### <a name="read"></a>Lesen

* Zurückgeben aller Datensätze
  * Die `OnAppearing`-Funktion von `BlogsPage.xaml.cs` gibt alle `Blog`-Datensätze zurück und speichert diese in einer `List`-Variable.

```csharp
using (var blogContext = new BloggingContext())
{
    var theBlogs = blogContext.Blogs.ToList();
}
```

* Zurückgeben spezifischer Datensätze
  * Die `OnAppearing`-Funktion von `PostsPage.xaml.cs` gibt `Post`-Datensätze zurück, die einen spezifischen `BlogId`-Wert enthalten.

```csharp
using (var blogContext = new BloggingContext())
{
    var postList = blogContext.Posts
        .Where(p => p.BlogId == BlogId)
        .ToList();
}
```

### <a name="create"></a>Erstellen

* Einfügen eines neuen Datensatzes
  * Die `Save_Clicked`-Funktion von `AddBlogPage.xaml.cs` fügt ein neues `Blog`-Objekt in die SQLite-Datenbank ein.

```csharp
var blog = new Blog { Url = blogUrl.Text };

using (var blogContext = new BloggingContext())
{
    blogContext.Add(blog);

    await blogContext.SaveChangesAsync();
}
```

### <a name="update"></a>Update

* Aktualisieren eines vorhandenen Datensatzes
  * Die `Save_Clicked`-Funktion von `AddPostPage.xaml.cs` aktualisiert ein vorhandenes `Blog`-Objekt mit einem neuen `Post`.

```csharp
var newPost = new Post
{
    BlogId = BlogId,
    Content = postCell.Text,
    Title = titleCell.Text
};

using (var blogContext = new BloggingContext())
{
    var blog = await blogContext
        .Blogs
        .FirstAsync(b => b.BlogId == BlogId);

    blog.Posts.Add(newPost);

    await blogContext.SaveChangesAsync();
}
```

### <a name="delete"></a>Löschen

* Löschen aller Datensätze mit einer Weitergabe an untergeordnete Datensätze
  * Die `DeleteAll_Clicked`-Funktion von `BlogsPage.xaml.cs` löscht alle `Blog`-Datensätze in der SQLite-Datenbank und gibt die Löschvorgänge an alle untergeordneten `Post`-Datensätze von `Blog` weiter.

```csharp
using (var blogContext = new BloggingContext())
{
    blogContext.RemoveRange(blogContext.Blogs);

    await blogContext.SaveChangesAsync();
}
```

## <a name="next-steps"></a>Nächste Schritte

In diesen ersten Schritten haben Sie gelernt, wie Sie eine Xamarin.Forms-Anwendung für den Zugriff auf eine SQLite-Datenbank mithilfe von Entity Framework Core verwenden.

Im Folgenden finden Sie weitere Entity Framework Core-Themen, die für Xamarin-Entwickler interessant sind:

* [Konfigurieren einer `DbContext`-Klasse](xref:core/dbcontext-configuration/index)
* Weitere Informationen zu [LINQ-Abfrageausdrücken](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)
* [Konfigurieren Sie das Modell](xref:core/modeling/index), um Aspekte wie [required](xref:core/modeling/entity-properties#required-and-optional-properties) (erforderlich) und [maximum length](xref:core/modeling/entity-properties#maximum-length) (maximale Länge) anzugeben.
