---
title: Gespeicherte Prozeduren mit mehreren Resultsets EF6
description: Gespeicherte Prozeduren mit mehreren Resultsets in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/advanced/multiple-result-sets
ms.openlocfilehash: 6b213b944ca06a7ca141746d86f1127be2fd7d87
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070223"
---
# <a name="stored-procedures-with-multiple-result-sets"></a>Gespeicherte Prozeduren mit mehreren Resultsets
Manchmal müssen Sie, wenn Sie gespeicherte Prozeduren verwenden, mehr als ein Resultset zurückgeben. Dieses Szenario wird häufig verwendet, um die Anzahl der Daten Bank Roundtrips zu reduzieren, die zum Verfassen eines einzelnen Bildschirms erforderlich sind.Vor EF5 konnte Entity Framework die gespeicherte Prozedur aufrufen, aber nur das erste Resultset an den aufrufenden Code zurückgeben.

In diesem Artikel werden zwei Möglichkeiten für den Zugriff auf mehr als ein Resultset aus einer gespeicherten Prozedur in Entity Framework erläutert. Eine, die nur Code verwendet und sowohl mit Code First als auch mit dem EF-Designer und einem, der nur mit dem EF-Designer funktioniert, funktioniert. Die Tools und API-Unterstützung für diese sollte in zukünftigen Versionen von Entity Framework verbessert werden.

## <a name="model"></a>Modellierung

In den Beispielen in diesem Artikel wird ein grundlegender Blog verwendet, bei dem ein Blog viele Beiträge enthält und ein Beitrag zu einem einzelnen Blog gehört. Wir verwenden eine gespeicherte Prozedur in der Datenbank, die alle Blogs und Beiträge wie folgt zurückgibt:

``` SQL
    CREATE PROCEDURE [dbo].[GetAllBlogsAndPosts]
    AS
        SELECT * FROM dbo.Blogs
        SELECT * FROM dbo.Posts
```

## <a name="accessing-multiple-result-sets-with-code"></a>Zugreifen auf mehrere Resultsets mit Code

Mithilfe von Code können Sie einen unformatierten SQL-Befehl ausführen, um die gespeicherte Prozedur auszuführen. Der Vorteil dieses Ansatzes besteht darin, dass er sowohl mit Code First als auch mit dem EF-Designer funktioniert.

Damit mehrere Resultsets funktionieren, müssen wir die ObjectContext-API mithilfe der iobjectcontextadapter-Schnittstelle löschen.

Nachdem wir einen ObjectContext haben, können wir die Übersetzung-Methode verwenden, um die Ergebnisse der gespeicherten Prozedur in Entitäten zu übersetzen, die in EF als normal verfolgt und verwendet werden können. Im folgenden Codebeispiel wird dies in Aktion veranschaulicht.

``` csharp
    using (var db = new BloggingContext())
    {
        // If using Code First we need to make sure the model is built before we open the connection
        // This isn't required for models created with the EF Designer
        db.Database.Initialize(force: false);

        // Create a SQL command to execute the sproc
        var cmd = db.Database.Connection.CreateCommand();
        cmd.CommandText = "[dbo].[GetAllBlogsAndPosts]";

        try
        {

            db.Database.Connection.Open();
            // Run the sproc
            var reader = cmd.ExecuteReader();

            // Read Blogs from the first result set
            var blogs = ((IObjectContextAdapter)db)
                .ObjectContext
                .Translate<Blog>(reader, "Blogs", MergeOption.AppendOnly);   


            foreach (var item in blogs)
            {
                Console.WriteLine(item.Name);
            }        

            // Move to second result set and read Posts
            reader.NextResult();
            var posts = ((IObjectContextAdapter)db)
                .ObjectContext
                .Translate<Post>(reader, "Posts", MergeOption.AppendOnly);


            foreach (var item in posts)
            {
                Console.WriteLine(item.Title);
            }
        }
        finally
        {
            db.Database.Connection.Close();
        }
    }
```

Die Translation-Methode akzeptiert den Reader, den wir beim Ausführen der Prozedur erhalten haben, einen EntitySet-Namen und eine MergeOption. Der EntitySet-Name ist identisch mit der dbset-Eigenschaft für den abgeleiteten Kontext. Die MergeOption-Aufzählung steuert, wie Ergebnisse behandelt werden, wenn die gleiche Entität bereits im Arbeitsspeicher vorhanden ist.

Hier durchlaufen wir die Auflistung von Blogs, bevor wir NextResult nennen. Dies ist für den obigen Code wichtig, da das erste Resultset vor dem Wechsel zum nächsten Resultset verarbeitet werden muss.

Nachdem die beiden Übersetzungsmethoden aufgerufen wurden, werden die Blog-und Post-Entitäten von EF auf die gleiche Weise wie jede andere Entität nachverfolgt und können daher geändert oder gelöscht und als normal gespeichert werden.

>[!NOTE]
> EF nimmt beim Erstellen von Entitäten mithilfe der Translation-Methode keine Zuordnung zu einem Konto vor. Es werden einfach die Spaltennamen im Resultset mit Eigenschaftsnamen für Ihre Klassen abgeglichen.

>[!NOTE]
> Wenn Sie Lazy Loading aktiviert haben und auf die Posts-Eigenschaft für eine der Blog Entitäten zugreifen, stellt EF eine Verbindung mit der Datenbank her, um alle Beiträge verzögert zu laden, obwohl wir Sie bereits alle geladen haben. Dies liegt daran, dass EF nicht weiß, ob Sie alle Beiträge geladen haben oder ob in der Datenbank weitere vorhanden sind. Wenn Sie dies vermeiden möchten, müssen Sie Lazy Loading deaktivieren.

## <a name="multiple-result-sets-with-configured-in-edmx"></a>Mehrere Resultsets mit Konfiguration in EDMX

>[!NOTE]
> Sie müssen .NET Framework 4,5 als Ziel festlegen, um mehrere Resultsets in edmx konfigurieren zu können. Wenn Sie .NET 4,0 als Ziel verwenden, können Sie die im vorherigen Abschnitt gezeigte Code basierte Methode verwenden.

Wenn Sie den EF-Designer verwenden, können Sie das Modell auch so ändern, dass es die unterschiedlichen Resultsets kennt, die zurückgegeben werden. Ein wichtiger Aspekt ist, dass die Tools nicht mehrere Resultsets unterstützen, sodass Sie die EDMX-Datei manuell bearbeiten müssen. Wenn Sie die EDMX-Datei wie folgt bearbeiten, wird auch die Überprüfung des Modells in vs nicht unterbricht. Wenn Sie also das Modell validieren, erhalten Sie immer Fehler.

-   Um dies zu erreichen, müssen Sie die gespeicherte Prozedur wie bei einer einzelnen resultsetabfrage zum Modell hinzufügen.
-   Wenn Sie diese haben, klicken Sie mit der rechten Maustaste auf das Modell, und wählen Sie **Öffnen mit aus.** **XML** -Datei

    ![Öffnen als](~/ef6/media/openas.png)

Nachdem Sie das Modell als XML geöffnet haben, müssen Sie die folgenden Schritte ausführen:

-   Suchen Sie den komplexen Typ und den Funktions Import in Ihrem Modell:

``` xml
    <!-- CSDL content -->
    <edmx:ConceptualModels>

    ...

      <FunctionImport Name="GetAllBlogsAndPosts" ReturnType="Collection(BlogModel.GetAllBlogsAndPosts_Result)" />

    ...

      <ComplexType Name="GetAllBlogsAndPosts_Result">
        <Property Type="Int32" Name="BlogId" Nullable="false" />
        <Property Type="String" Name="Name" Nullable="false" MaxLength="255" />
        <Property Type="String" Name="Description" Nullable="true" />
      </ComplexType>

    ...

    </edmx:ConceptualModels>
```

 

-   Entfernen des komplexen Typs
-   Aktualisieren Sie den Funktions Import so, dass er ihren Entitäten zugeordnet wird. in diesem Fall sieht er wie folgt aus:

``` xml
    <FunctionImport Name="GetAllBlogsAndPosts">
      <ReturnType EntitySet="Blogs" Type="Collection(BlogModel.Blog)" />
      <ReturnType EntitySet="Posts" Type="Collection(BlogModel.Post)" />
    </FunctionImport>
```

Dadurch wird dem Modell mitgeteilt, dass die gespeicherte Prozedur zwei Auflistungen zurückgibt: einen der Blogeinträge und einen der Post-Einträge.

-   Suchen Sie das Funktions Zuordnungs Element:

``` xml
    <!-- C-S mapping content -->
    <edmx:Mappings>

    ...

      <FunctionImportMapping FunctionImportName="GetAllBlogsAndPosts" FunctionName="BlogModel.Store.GetAllBlogsAndPosts">
        <ResultMapping>
          <ComplexTypeMapping TypeName="BlogModel.GetAllBlogsAndPosts_Result">
            <ScalarProperty Name="BlogId" ColumnName="BlogId" />
            <ScalarProperty Name="Name" ColumnName="Name" />
            <ScalarProperty Name="Description" ColumnName="Description" />
          </ComplexTypeMapping>
        </ResultMapping>
      </FunctionImportMapping>

    ...

    </edmx:Mappings>
```

-   Ersetzen Sie die Ergebnis Zuordnung durch eine für jede Entität, die zurückgegeben wird, wie z. b. Folgendes:

``` xml
    <ResultMapping>
      <EntityTypeMapping TypeName ="BlogModel.Blog">
        <ScalarProperty Name="BlogId" ColumnName="BlogId" />
        <ScalarProperty Name="Name" ColumnName="Name" />
        <ScalarProperty Name="Description" ColumnName="Description" />
      </EntityTypeMapping>
    </ResultMapping>
    <ResultMapping>
      <EntityTypeMapping TypeName="BlogModel.Post">
        <ScalarProperty Name="BlogId" ColumnName="BlogId" />
        <ScalarProperty Name="PostId" ColumnName="PostId"/>
        <ScalarProperty Name="Title" ColumnName="Title" />
        <ScalarProperty Name="Text" ColumnName="Text" />
      </EntityTypeMapping>
    </ResultMapping>
```

Es ist auch möglich, die Resultsets komplexen Typen zuzuordnen, z. b. der standardmäßig erstellten. Zu diesem Zweck erstellen Sie einen neuen komplexen Typ, anstatt ihn zu entfernen, und verwenden die komplexen Typen überall dort, wo Sie die Entitäts Namen in den obigen Beispielen verwendet haben.

Nachdem diese Zuordnungen geändert wurden, können Sie das Modell speichern und den folgenden Code ausführen, um die gespeicherte Prozedur zu verwenden:

``` csharp
    using (var db = new BlogEntities())
    {
        var results = db.GetAllBlogsAndPosts();

        foreach (var result in results)
        {
            Console.WriteLine("Blog: " + result.Name);
        }

        var posts = results.GetNextResult<Post>();

        foreach (var result in posts)
        {
            Console.WriteLine("Post: " + result.Title);
        }

        Console.ReadLine();
    }
```

>[!NOTE]
> Wenn Sie die EDMX-Datei für das Modell manuell bearbeiten, wird sie überschrieben, wenn Sie das Modell jemals aus der Datenbank generieren.

## <a name="summary"></a>Zusammenfassung

Hier haben wir zwei verschiedene Methoden für den Zugriff auf mehrere Resultsets mit Entity Framework gezeigt. Beide sind abhängig von ihrer Situation und ihren Vorlieben gleichermaßen gültig, und Sie sollten den Wert auswählen, der für ihre Umstände am besten geeignet ist. Es ist geplant, dass die Unterstützung für mehrere Resultsets in zukünftigen Versionen von Entity Framework verbessert wird und dass das Ausführen der Schritte in diesem Dokument nicht mehr erforderlich ist.
