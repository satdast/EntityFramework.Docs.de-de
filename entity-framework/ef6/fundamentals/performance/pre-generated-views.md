---
title: Vorab generierte Mapping-Sichten-EF6
description: Vorab generierte Mapping-Sichten in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/performance/pre-generated-views
ms.openlocfilehash: b69c1eea1e219d76a8de1b2cf3b88de2cf74c8f0
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073551"
---
# <a name="pre-generated-mapping-views"></a>Vorab generierte Mapping-Sichten
Bevor die Entity Framework eine Abfrage ausführen oder Änderungen an der Datenquelle speichern kann, muss Sie einen Satz von zuordnungssichten generieren, um auf die Datenbank zuzugreifen. Diese Zuweisungs Sichten sind eine Reihe von Entity SQL-Anweisungen, die die Datenbank auf abstrakte Weise darstellen. Sie sind Teil der Metadaten, die pro Anwendungsdomäne zwischengespeichert werden. Wenn Sie mehrere Instanzen desselben Kontexts in derselben Anwendungsdomäne erstellen, werden die Zuordnung von Sichten aus den zwischengespeicherten Metadaten wieder verwendet, anstatt Sie neu zu generieren. Da die Generierung der Ansichts Generierung ein wesentlicher Bestandteil der Gesamtkosten für die Ausführung der ersten Abfrage ist, ermöglicht Ihnen die Entity Framework, die Zuordnung von Sichten vorab zu generieren und Sie in das kompilierte Projekt einzubeziehen.Weitere Informationen finden Sie unter  [Überlegungen zur Leistung (Entity Framework)](xref:ef6/fundamentals/performance/perf-whitepaper).

## <a name="generating-mapping-views-with-the-ef-power-tools-community-edition"></a>Erstellen von zuordnungssichten mit der Entity Edition von EF Power Tools

Die einfachste Möglichkeit zum vorab Generieren von Ansichten ist die Verwendung der [EF Power Tools Community Edition](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition). Nachdem Sie die Power Tools installiert haben, verfügen Sie über eine Menüoption zum Generieren von Ansichten, wie unten beschrieben.

-   Klicken Sie für **Code First** Modelle mit der rechten Maustaste auf die Codedatei, in der die dbcontext-Klasse enthalten ist.
-   Klicken Sie bei **EF-Designer** -Modellen mit der rechten Maustaste auf die EDMX-Datei.

![Generieren von Sichten](~/ef6/media/generateviews.png)

Nachdem der Vorgang abgeschlossen ist, verfügen Sie über eine Klasse, die der folgenden generierten ähnelt:

![generierte Sichten](~/ef6/media/generatedviews.png)

Wenn Sie nun die Anwendung ausführen, verwendet EF diese Klasse, um Ansichten nach Bedarf zu laden. Wenn sich das Modell ändert und Sie diese Klasse nicht erneut generieren, löst EF eine Ausnahme aus.

## <a name="generating-mapping-views-from-code---ef6-onwards"></a>Erstellen von Mapping-Sichten aus Code EF6 ab

Die andere Möglichkeit zum Generieren von Sichten besteht darin, die von EF bereitgestellten APIs zu verwenden. Wenn Sie diese Methode verwenden, haben Sie die Möglichkeit, die Ansichten beliebig zu serialisieren. Sie müssen die Ansichten jedoch auch selbst laden.

> [!NOTE]
> **Nur EF6** : die in diesem Abschnitt gezeigten APIs wurden in Entity Framework 6 eingeführt. Wenn Sie eine frühere Version verwenden, gelten diese Informationen nicht.

### <a name="generating-views"></a>Generieren von Ansichten

Die APIs zum Generieren von Sichten befinden sich in der System. Data. Entity. Core. Mapping. StorageMappingItemCollection-Klasse. Sie können eine storagemappingcollection für einen Kontext abrufen, indem Sie die MetadataWorkspace eines ObjectContext verwenden. Wenn Sie die neuere dbcontext-API verwenden, können Sie mit dem iobjectcontextadapter wie unten darauf zugreifen. in diesem Code verfügen wir über eine Instanz des abgeleiteten dbcontext namens dbcontext:

``` csharp
    var objectContext = ((IObjectContextAdapter) dbContext).ObjectContext;
    var  mappingCollection = (StorageMappingItemCollection)objectContext.MetadataWorkspace
                                                                        .GetItemCollection(DataSpace.CSSpace);
```

Sobald Sie über die StorageMappingItemCollection verfügen, können Sie Zugriff auf die Methoden GenerateViews und computemappinghashvalue erhalten.

``` csharp
    public Dictionary\<EntitySetBase, DbMappingView> GenerateViews(IList<EdmSchemaError> errors)
    public string ComputeMappingHashValue()
```

Die erste Methode erstellt ein Wörterbuch mit einem Eintrag für jede Ansicht in der Container Zuordnung. Die zweite Methode berechnet einen Hashwert für die einzelne Container Zuordnung und wird zur Laufzeit verwendet, um zu überprüfen, ob das Modell nicht geändert wurde, seit die Ansichten vorab generiert wurden. Über schreibungen der beiden Methoden werden für komplexe Szenarien mit mehreren Container Zuordnungen bereitgestellt.

Beim Generieren von Sichten rufen Sie die GenerateViews-Methode auf und schreiben dann die resultierenden EntitySetBase und dbmappingview. Außerdem müssen Sie den von der computemappinghashvalue-Methode generierten Hash speichern.

### <a name="loading-views"></a>Sichten werden geladen

Um die von der GenerateViews-Methode generierten Sichten zu laden, können Sie EF eine Klasse bereitstellen, die von der abstrakten Klasse dbmappingviewcache erbt. Dbmappingviewcache gibt zwei Methoden an, die Sie implementieren müssen:

``` csharp
    public abstract string MappingHashValue { get; }
    public abstract DbMappingView GetView(EntitySetBase extent);
```

Die mappinghashvalue-Eigenschaft muss den von der computemappinghashvalue-Methode generierten Hash zurückgeben. Wenn EF nach Sichten fragt, wird zunächst der Hashwert des Modells mit dem von dieser Eigenschaft zurückgegebenen Hash generiert und verglichen. Wenn keine Entsprechung gefunden wird, löst EF eine EntityCommandCompilationException-Ausnahme aus.

Die GetView-Methode akzeptiert ein EntitySetBase-Objekt, und Sie müssen eine dbmappingview mit der EntitySql-ID zurückgeben, die für generiert wurde, die dem angegebenen EntitySetBase in dem von der GenerateViews-Methode generierten Wörterbuch zugeordnet ist. Wenn EF nach einer Ansicht fragt, die Sie nicht haben, sollte GetView NULL zurückgeben.

Im folgenden finden Sie eine Extraktion aus dbmappingviewcache, die mit den oben beschriebenen Power Tools generiert wird. darin wird eine Möglichkeit zum Speichern und Abrufen des erforderlichen EntitySql-Werts angezeigt.

``` csharp
    public override string MappingHashValue
    {
        get { return "a0b843f03dd29abee99789e190a6fb70ce8e93dc97945d437d9a58fb8e2afd2e"; }
    }

    public override DbMappingView GetView(EntitySetBase extent)
    {
        if (extent == null)
        {
            throw new ArgumentNullException("extent");
        }

        var extentName = extent.EntityContainer.Name + "." + extent.Name;

        if (extentName == "BlogContext.Blogs")
        {
            return GetView2();
        }

        if (extentName == "BlogContext.Posts")
        {
            return GetView3();
        }

        return null;
    }

    private static DbMappingView GetView2()
    {
        return new DbMappingView(@"
            SELECT VALUE -- Constructing Blogs
            [BlogApp.Models.Blog](T1.Blog_BlogId, T1.Blog_Test, T1.Blog_title, T1.Blog_Active, T1.Blog_SomeDecimal)
            FROM (
            SELECT
                T.BlogId AS Blog_BlogId,
                T.Test AS Blog_Test,
                T.title AS Blog_title,
                T.Active AS Blog_Active,
                T.SomeDecimal AS Blog_SomeDecimal,
                True AS _from0
            FROM CodeFirstDatabase.Blog AS T
            ) AS T1");
    }
```

Damit EF ihren dbmappingviewcache verwenden kann, fügen Sie das dbmappingviewcachetypeattribute-Attribut hinzu, indem Sie den Kontext angeben, für den es erstellt wurde. Im folgenden Code ordnen wir den blogcontext der mymappingviewcache-Klasse zu.

``` csharp
    [assembly: DbMappingViewCacheType(typeof(BlogContext), typeof(MyMappingViewCache))]
```

Für komplexere Szenarien können Zuordnungs Ansichts Cache Instanzen bereitgestellt werden, indem eine Zuordnungs Ansichts Cache-Factory angegeben wird. Dies kann durch Implementieren der abstrakten Klasse System. Data. Entity. Infrastructure. mappingviews. dbmappingviewcachefactory erreicht werden. Die Instanz der Cache Factory der Zuordnungs Ansicht, die verwendet wird, kann mithilfe von StorageMappingItemCollection. mappingviewcachefactoryproperty abgerufen oder festgelegt werden.
