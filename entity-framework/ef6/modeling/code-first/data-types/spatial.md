---
title: Spatial-Code First-EF6
description: Räumliche Code First in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/data-types/spatial
ms.openlocfilehash: 6942829809e37b5d62f61488ff6a190c8a38febb
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065237"
---
# <a name="spatial---code-first"></a>Räumliche Code First
> [!NOTE]
> **Nur EF5** : die Features, APIs usw., die auf dieser Seite erläutert wurden, wurden in Entity Framework 5 eingeführt. Wenn Sie eine frühere Version verwenden, gelten manche Informationen nicht.

Das Video und die schrittweise exemplarische Vorgehensweise zeigen, wie räumliche Typen mit Entity Framework Code First zugeordnet werden. Außerdem wird veranschaulicht, wie eine LINQ-Abfrage verwendet wird, um einen Abstand zwischen zwei Standorten zu ermitteln.

In dieser exemplarischen Vorgehensweise wird Code First verwendet, um eine neue Datenbank zu erstellen, aber Sie können [Code First auch für eine vorhandene Datenbank](xref:ef6/modeling/code-first/workflows/existing-database)verwenden.

Die Unterstützung räumlicher Typen wurde in Entity Framework 5 eingeführt. Beachten Sie, dass Sie für die Verwendung der neuen Funktionen, wie z. b. räumlichem Typ, aufzählen und Tabellenwert Funktionen, auf .NET Framework 4,5 abzielen müssen. Visual Studio 2012 hat standardmäßig .NET 4,5 als Ziel.

Um räumliche Datentypen zu verwenden, müssen Sie auch einen Entity Framework Anbieter verwenden, der über räumliche Unterstützung verfügt. Weitere Informationen finden Sie [unter Anbieter Unterstützung für räumliche Typen](xref:ef6/fundamentals/providers/spatial-support) .

Es gibt zwei Haupt Datentypen für räumliche Daten: Geography und Geometry. Der geography-Datentyp speichert Ellipsen Daten (z. b. GPS-breiten-und Längenkoordinaten). Der geometry-Datentyp stellt das euklidische (flache) Koordinatensystem dar.

## <a name="watch-the-video"></a>Video ansehen
In diesem Video wird gezeigt, wie räumliche Typen mit Entity Framework Code First zugeordnet werden. Außerdem wird veranschaulicht, wie eine LINQ-Abfrage verwendet wird, um einen Abstand zwischen zwei Standorten zu ermitteln.

**Präsentiert von**: Julia kornich

**Video**: [WMV](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.wmv)  |  [MP4](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-mp4video-spatialwithcodefirst.m4v)  |  [WMV (zip)](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.zip)

## <a name="pre-requisites"></a>Voraussetzungen

Sie müssen Visual Studio 2012, Ultimate, Premium, Professional oder Web Express Edition installiert haben, um diese exemplarische Vorgehensweise abzuschließen.

## <a name="set-up-the-project"></a>Einrichten des Projekts

1.  Öffnen Sie Visual Studio 2012
2.  Zeigen Sie im Menü **Datei** auf **neu**, und klicken Sie dann auf **Projekt** .
3.  Klicken Sie im linken Bereich auf **Visual C \# **, und wählen Sie dann die **Konsolen** Vorlage aus.
4.  Geben Sie **spatialcode First** als Namen für das Projekt ein, und klicken Sie auf **OK** .

## <a name="define-a-new-model-using-code-first"></a>Definieren eines neuen Modells mit Code First

Wenn Sie Code First Entwicklung verwenden, schreiben Sie in der Regel .NET Framework Klassen, die ihr konzeptionelles (Domänen-) Modell definieren. Der folgende Code definiert die University-Klasse.

Die University verfügt über die Location-Eigenschaft des dbgeography-Typs. Wenn Sie den dbgeography-Typ verwenden möchten, müssen Sie einen Verweis auf die System. Data. Entity-Assembly hinzufügen und auch die System. Data. Spatial using-Anweisung hinzufügen.

Öffnen Sie die Datei Program.cs, und fügen Sie die folgenden using-Anweisungen am Anfang der Datei ein:

``` csharp
using System.Data.Spatial;
```

Fügen Sie der Program.cs-Datei die folgende Definition der University-Klasse hinzu.

``` csharp
public class University  
{
    public int UniversityID { get; set; }
    public string Name { get; set; }
    public DbGeography Location { get; set; }
}
```

## <a name="define-the-dbcontext-derived-type"></a>Definieren des abgeleiteten dbcontext-Typs

Zusätzlich zur Definition von Entitäten müssen Sie eine Klasse definieren, die von DbContext abgeleitet ist und DbSet&lt;TEntity&gt;-Eigenschaften bereitstellt. Die DbSet&lt;TEntity&gt;-Eigenschaften informieren den Kontext darüber, welche Typen Sie in das Modell einbeziehen möchten.

Eine Instanz des von DbContext abgeleiteten Typs verwaltet die Entitätsobjekte während der Laufzeit, was das Auffüllen der Objekte mit Daten aus einer Datenbank, die Änderungsnachverfolgung und das persistente Speichern von Daten in der Datenbank umfasst.

Die Typen "dbcontext" und "dbset" werden in der EntityFramework-Assembly definiert. Wir fügen mit dem nuget-Paket "EntityFramework" einen Verweis auf diese dll hinzu.

1.  Klicken Sie in Projektmappen-Explorer mit der rechten Maustaste auf den Projektnamen.
2.  Wählen Sie **nuget-Pakete verwalten... aus.**
3.  Wählen Sie im Dialogfeld nuget-Pakete verwalten die Registerkarte **Online** aus, und wählen Sie das Paket **EntityFramework** aus.
4.  Klicken Sie auf **Install** (Installieren).

Beachten Sie, dass zusätzlich zur EntityFramework-Assembly auch ein Verweis auf die System. ComponentModel. DataAnnotations-Assembly hinzugefügt wird.

Fügen Sie am Anfang der Program.cs-Datei die folgende using-Anweisung hinzu:

``` csharp
using System.Data.Entity;
```

Fügen Sie in Program.cs die Kontext Definition hinzu. 

``` csharp
public partial class UniversityContext : DbContext
{
    public DbSet<University> Universities { get; set; }
}
```

## <a name="persist-and-retrieve-data"></a>Persistenz und Abrufen von Daten

Öffnen Sie die Datei Program.cs, in der die Main-Methode definiert ist. Fügen Sie der Main-Funktion den folgenden Code hinzu.

Der Code fügt dem Kontext zwei neue University-Objekte hinzu. Räumliche Eigenschaften werden mithilfe der Methode "dbgeography. fromtext" initialisiert. Der als wellknowntext dargestellte geografiepunkt wird an die-Methode übermittelt. Der Code speichert die Daten dann. Anschließend wird die LINQ-Abfrage, die ein University-Objekt zurückgibt, dessen Standort dem angegebenen Speicherort am nächsten liegt, erstellt und ausgeführt.

``` csharp
using (var context = new UniversityContext ())
{
    context.Universities.Add(new University()
        {
            Name = "Graphic Design Institute",
            Location = DbGeography.FromText("POINT(-122.336106 47.605049)"),
        });

    context. Universities.Add(new University()
        {
            Name = "School of Fine Art",
            Location = DbGeography.FromText("POINT(-122.335197 47.646711)"),
        });

    context.SaveChanges();

    var myLocation = DbGeography.FromText("POINT(-122.296623 47.640405)");

    var university = (from u in context.Universities
                        orderby u.Location.Distance(myLocation)
                        select u).FirstOrDefault();

    Console.WriteLine(
        "The closest University to you is: {0}.",
        university.Name);
}
```

Kompilieren Sie die Anwendung, und führen Sie sie aus. Das Programm erzeugt die folgende Ausgabe:

```console
The closest University to you is: School of Fine Art.
```

## <a name="view-the-generated-database"></a>Anzeigen der generierten Datenbank

Wenn Sie die Anwendung zum ersten Mal ausführen, erstellt die Entity Framework eine Datenbank für Sie. Da Visual Studio 2012 installiert ist, wird die Datenbank auf der localdb-Instanz erstellt. Standardmäßig benennt die-Entity Framework die Datenbank nach dem voll qualifizierten Namen des abgeleiteten Kontexts (in diesem Beispiel " **spatialcodefirst. universitycontext**"). Nachfolgend wird die vorhandene Datenbank verwendet.  

Beachten Sie Folgendes: Wenn Sie Änderungen am Modell vornehmen, nachdem die Datenbank erstellt wurde, sollten Sie Code First-Migrationen zum Aktualisieren des Datenbankschemas verwenden. Ein Beispiel für die Verwendung von Migrationen finden Sie [unter Code First einer neuen Datenbank](xref:ef6/modeling/code-first/workflows/new-database) .

Gehen Sie folgendermaßen vor, um die Datenbank und die Daten anzuzeigen:

1.  Wählen Sie im Hauptmenü von Visual Studio 2012 die Option SQL Server-Objekt-Explorer **anzeigen** aus  - &gt; **SQL Server Object Explorer**.
2.  Wenn localdb nicht in der Liste der Server enthalten ist, klicken Sie auf **SQL Server** mit der rechten Maustaste, und wählen Sie **hinzu SQL Server fügen** aus, um die Verbindung mit der localdb-Instanz mit der standardmäßigen **Windows-Authentifizierung** herzustellen.
3.  Erweitern Sie den Knoten localdb.
4.  Erweitern Sie den Ordner **Datenbanken** , um die neue Datenbank anzuzeigen und zur Tabelle **Universitäten** zu navigieren.
5.  Um Daten anzuzeigen, klicken Sie mit der rechten Maustaste auf die Tabelle, und wählen Sie **Daten anzeigen**

## <a name="summary"></a>Zusammenfassung

In dieser exemplarischen Vorgehensweise wurde erläutert, wie räumliche Typen mit Entity Framework Code First verwendet werden. 
