---
title: Spatial-EF-Designer-EF6
description: Spatial-EF-Designer in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/data-types/spatial
ms.openlocfilehash: 6524e3d2be6d5028ab12d6bfd7c65ce3a232a07a
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073083"
---
# <a name="spatial---ef-designer"></a>Räumlich: EF-Designer
> [!NOTE]
> **Nur EF5** : die Features, APIs usw., die auf dieser Seite erläutert wurden, wurden in Entity Framework 5 eingeführt. Wenn Sie eine frühere Version verwenden, gelten manche Informationen nicht.

Im Video und in der exemplarischen Vorgehensweise wird gezeigt, wie räumliche Typen mit dem Entity Framework Designer zugeordnet werden. Außerdem wird veranschaulicht, wie eine LINQ-Abfrage verwendet wird, um einen Abstand zwischen zwei Standorten zu ermitteln.

In dieser exemplarischen Vorgehensweise wird Model First verwendet, um eine neue Datenbank zu erstellen, aber der EF-Designer kann auch mit dem [Database First](xref:ef6/modeling/designer/workflows/database-first) Workflow verwendet werden, um eine Zuordnung zu einer vorhandenen Datenbank herzustellen.

Die Unterstützung räumlicher Typen wurde in Entity Framework 5 eingeführt. Beachten Sie, dass Sie für die Verwendung der neuen Funktionen, wie z. b. räumlichem Typ, aufzählen und Tabellenwert Funktionen, auf .NET Framework 4,5 abzielen müssen. Visual Studio 2012 hat standardmäßig .NET 4,5 als Ziel.

Um räumliche Datentypen zu verwenden, müssen Sie auch einen Entity Framework Anbieter verwenden, der über räumliche Unterstützung verfügt. Weitere Informationen finden Sie [unter Anbieter Unterstützung für räumliche Typen](xref:ef6/fundamentals/providers/spatial-support) .

Es gibt zwei Haupt Datentypen für räumliche Daten: Geography und Geometry. Der geography-Datentyp speichert Ellipsen Daten (z. b. GPS-breiten-und Längenkoordinaten). Der geometry-Datentyp stellt das euklidische (flache) Koordinatensystem dar.

## <a name="watch-the-video"></a>Video ansehen
In diesem Video wird gezeigt, wie räumliche Typen dem Entity Framework Designer zugeordnet werden. Außerdem wird veranschaulicht, wie eine LINQ-Abfrage verwendet wird, um einen Abstand zwischen zwei Standorten zu ermitteln.

**Präsentiert von**: Julia kornich

**Video**: [WMV](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.wmv)  |  [MP4](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-mp4video-spatialwithdesigner.m4v)  |  [WMV (zip)](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.zip)

## <a name="pre-requisites"></a>Voraussetzungen

Sie müssen Visual Studio 2012, Ultimate, Premium, Professional oder Web Express Edition installiert haben, um diese exemplarische Vorgehensweise abzuschließen.

## <a name="set-up-the-project"></a>Einrichten des Projekts

1.  Öffnen Sie Visual Studio 2012
2.  Zeigen Sie im Menü **Datei** auf **neu**, und klicken Sie dann auf **Projekt** .
3.  Klicken Sie im linken Bereich auf **Visual C \# **, und wählen Sie dann die **Konsolen** Vorlage aus.
4.  Geben Sie **spatialefdesigner** als Namen für das Projekt ein, und klicken Sie auf **OK** .

## <a name="create-a-new-model-using-the-ef-designer"></a>Erstellen eines neuen Modells mit dem EF-Designer

1.  Klicken Sie mit der rechten Maustaste auf den Projektnamen Projektmappen-Explorer, zeigen Sie auf **Hinzufügen**, und klicken Sie dann auf **Neues Element**
2.  Wählen Sie im linken Menü **Daten** aus, und wählen Sie dann im Bereich Vorlagen die Option **ADO.NET Entity Data Model** aus.
3.  Geben Sie für den Dateinamen " **universitymodel. edmx** " ein, und klicken Sie auf **Hinzufügen** .
4.  Wählen Sie auf der Seite des Entity Data Model-Assistenten im Dialogfeld Modell Inhalte auswählen die Option **leeres Modell** aus.
5.  Klicken Sie auf **Fertig stellen**.

Die Entity Designer, die eine Entwurfs Oberfläche zum Bearbeiten des Modells bereitstellt, wird angezeigt.

Der Assistent führt die folgenden Aktionen aus:

-   Generiert die Datei "enumtestmodel. edmx", die das konzeptionelle Modell, das Speichermodell und die Zuordnung zwischen Ihnen definiert. Legt die metadatenartefaktverarbeitungs-Eigenschaft der EDMX-Datei fest, die in die Ausgabeassembly eingebettet werden soll, sodass die generierten Metadatendateien in die Assembly eingebettet werden
-   Fügt einen Verweis auf die folgenden Assemblys hinzu: EntityFramework, System. ComponentModel. DataAnnotations und System. Data. Entity.
-   Erstellt UniversityModel.TT-und UniversityModel.Context.tt-Dateien und fügt Sie der EDMX-Datei hinzu. Diese T4-Vorlagen Dateien generieren den Code, der den von dbcontext abgeleiteten Typ und poco-Typen definiert, die den Entitäten im edmx-Modell zugeordnet werden.

## <a name="add-a-new-entity-type"></a>Neuen Entitätstyp hinzufügen

1.  Klicken Sie mit der rechten Maustaste auf einen leeren Bereich der Entwurfs Oberfläche, und wählen Sie **Add- &gt; Entity**, das Dialogfeld neue Entität wird angezeigt.
2.  Geben Sie **University** **als Typnamen** an, und geben Sie für den Schlüssel Eigenschaftsnamen die Angabe " **universityid** " an.
3.  Klicken Sie auf **OK**
4.  Klicken Sie mit der rechten Maustaste auf die Entität, und wählen Sie **Add New- &gt; Scalar**
5.  Benennen Sie die neue Eigenschaft in **Name** um.
6.  Fügen Sie eine weitere skalare Eigenschaft hinzu, und benennen Sie Sie in **Location** um. Öffnen Sie die Eigenschaftenfenster, und ändern Sie den Typ der neuen Eigenschaft in **geography**
7.  Speichern Sie das Modell, und erstellen Sie das Projekt.
    > [!NOTE]
    > Wenn Sie erstellen, werden möglicherweise Warnungen zu nicht zugeordneten Entitäten und Zuordnungen in der Fehlerliste angezeigt. Sie können diese Warnungen ignorieren, da die Fehler nach dem Generieren der Datenbank aus dem Modell entfernt werden.

## <a name="generate-database-from-model"></a>Datenbank aus Modell generieren

Nun können wir eine Datenbank generieren, die auf dem Modell basiert.

1.  Klicken Sie mit der rechten Maustaste auf einen leeren Bereich auf der Entity Designer Oberfläche, und wählen Sie **Datenbank aus Modell generieren** .
2.  Das Dialog Feld Wählen Sie Ihre Datenverbindung aus des Assistenten zum Generieren von Datenbanken wird angezeigt. Klicken Sie auf die Schaltfläche **neue Verbindung** , und **Geben Sie** **(localdb) \\ mssqllocaldb** für den Servernamen und die **University** für die Datenbank an.
3.  Wenn Sie in einem Dialogfeld gefragt werden, ob Sie eine neue Datenbank erstellen möchten, klicken Sie auf **Ja**.
4.  Klicken Sie auf **weiter** , und der Assistent zum Erstellen einer Datenbank generiert die Datendefinitionssprache (DDL) zum Erstellen einer Datenbank. die generierte DDL wird im Dialog Feld Zusammenfassung und Einstellungen angezeigt. Hinweis: die DDL enthält keine Definition für eine Tabelle, die dem Enumerationstyp zugeordnet ist.
5.  Klicken Sie auf **Fertig** stellen klicken Sie das DDL-Skript nicht aus.
6.  Der Assistent zum Erstellen einer Datenbank führt Folgendes aus: öffnet die Datei " **universitymodel. edmx. SQL** " im T-SQL-Editor App.config, generiert die Abschnitte "Store Schema" und "Mapping" der EDMX-Datei.
7.  Klicken Sie im T-SQL-Editor auf die Rechte Maustaste, und wählen Sie das Dialogfeld Verbindung mit Server herstellen aus, und **Geben Sie** die **Connect** Verbindungsinformationen aus Schritt 2 ein.
8.  Um das generierte Schema anzuzeigen, klicken Sie in SQL Server-Objekt-Explorer mit der rechten Maustaste auf den Datenbanknamen, und wählen Sie **Aktualisieren** .

## <a name="persist-and-retrieve-data"></a>Persistenz und Abrufen von Daten

Öffnen Sie die Datei Program.cs, in der die Main-Methode definiert ist. Fügen Sie der Main-Funktion den folgenden Code hinzu.

Der Code fügt dem Kontext zwei neue University-Objekte hinzu. Räumliche Eigenschaften werden mithilfe der Methode "dbgeography. fromtext" initialisiert. Der als wellknowntext dargestellte geografiepunkt wird an die-Methode übermittelt. Der Code speichert die Daten dann. Anschließend wird die LINQ-Abfrage, die ein University-Objekt zurückgibt, dessen Standort dem angegebenen Speicherort am nächsten liegt, erstellt und ausgeführt.

``` csharp
using (var context = new UniversityModelContainer())
{
    context.Universities.Add(new University()
    {
        Name = "Graphic Design Institute",
        Location = DbGeography.FromText("POINT(-122.336106 47.605049)"),
    });

    context.Universities.Add(new University()
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

Zum Anzeigen von Daten in der Datenbank klicken Sie in SQL Server-Objekt-Explorer mit der rechten Maustaste auf den Datenbanknamen, und wählen Sie **Aktualisieren**aus. Klicken Sie dann auf die Rechte Maustaste in der Tabelle, und wählen Sie **Daten anzeigen**aus.

## <a name="summary"></a>Zusammenfassung

In dieser exemplarischen Vorgehensweise wurde erläutert, wie räumliche Typen mithilfe der Entity Framework Designer zugeordnet werden und wie räumliche Typen im Code verwendet werden. 
