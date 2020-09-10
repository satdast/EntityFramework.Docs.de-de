---
title: Aufzählungs Unterstützung-EF-Designer-EF6
description: Aufzählungs Unterstützung-EF-Designer in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: c6ae6d8f-1ace-47db-ad47-b1718f1ba082
uid: ef6/modeling/designer/data-types/enums
ms.openlocfilehash: 316e8160cfd69d9912d63260471eee3df0eb58f6
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620509"
---
# <a name="enum-support---ef-designer"></a>Aufzählungs Unterstützung-EF-Designer
> [!NOTE]
> **Nur EF5** : die Features, APIs usw., die auf dieser Seite erläutert wurden, wurden in Entity Framework 5 eingeführt. Wenn Sie eine frühere Version verwenden, gelten manche Informationen nicht.

In diesem Video und der schrittweisen exemplarischen Vorgehensweise wird die Verwendung von Enumerationstypen mit dem Entity Framework Designer erläutert. Außerdem wird veranschaulicht, wie Sie-auf--Aufstände in einer LINQ-Abfrage verwenden.

In dieser exemplarischen Vorgehensweise wird Model First verwendet, um eine neue Datenbank zu erstellen, aber der EF-Designer kann auch mit dem [Database First](xref:ef6/modeling/designer/workflows/database-first) Workflow verwendet werden, um eine Zuordnung zu einer vorhandenen Datenbank herzustellen.

Die Aufzählungs Unterstützung wurde in Entity Framework 5 eingeführt. Wenn Sie die neuen Funktionen wie Enumerationstypen, räumliche Datentypen und Tabellenwert Funktionen verwenden möchten, müssen Sie .NET Framework 4,5-Zielversion verwenden. Visual Studio 2012 hat standardmäßig .NET 4,5 als Ziel.

In Entity Framework kann eine Enumeration die folgenden zugrunde liegenden Typen aufweisen: **Byte**, **Int16**, **Int32**, **Int64** oder **SByte**.

## <a name="watch-the-video"></a>Video ansehen
In diesem Video wird gezeigt, wie Enumerationstypen mit dem Entity Framework Designer verwendet werden. Außerdem wird veranschaulicht, wie Sie-auf--Aufstände in einer LINQ-Abfrage verwenden.

**Präsentiert von**: Julia kornich

**Video**: [WMV](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.wmv)  |  [MP4](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-mp4video-enumwithdesiger.m4v)  |  [WMV (zip)](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.zip)

## <a name="pre-requisites"></a>Voraussetzungen

Sie müssen Visual Studio 2012, Ultimate, Premium, Professional oder Web Express Edition installiert haben, um diese exemplarische Vorgehensweise abzuschließen.

## <a name="set-up-the-project"></a>Einrichten des Projekts

1.  Öffnen Sie Visual Studio 2012
2.  Zeigen Sie im Menü **Datei** auf **neu**, und klicken Sie dann auf **Projekt** .
3.  Klicken Sie im linken Bereich auf **Visual C \# **, und wählen Sie dann die **Konsolen** Vorlage aus.
4.  Geben Sie **enumef Designer** als Namen für das Projekt ein, und klicken Sie auf **OK** .

## <a name="create-a-new-model-using-the-ef-designer"></a>Erstellen eines neuen Modells mit dem EF-Designer

1.  Klicken Sie mit der rechten Maustaste auf den Projektnamen Projektmappen-Explorer, zeigen Sie auf **Hinzufügen**, und klicken Sie dann auf **Neues Element**
2.  Wählen Sie im linken Menü **Daten** aus, und wählen Sie dann im Bereich Vorlagen die Option **ADO.NET Entity Data Model** aus.
3.  Geben Sie als Dateiname **enumtestmodel. edmx** ein, und klicken Sie dann auf **Hinzufügen** .
4.  Wählen Sie auf der Seite des Entity Data Model-Assistenten im Dialogfeld Modell Inhalte auswählen die Option **leeres Modell** aus.
5.  Klicken Sie auf **Fertig stellen**.

Die Entity Designer, die eine Entwurfs Oberfläche zum Bearbeiten des Modells bereitstellt, wird angezeigt.

Der Assistent führt die folgenden Aktionen aus:

-   Generiert die Datei "enumtestmodel. edmx", die das konzeptionelle Modell, das Speichermodell und die Zuordnung zwischen Ihnen definiert. Legt die metadatenartefaktverarbeitungs-Eigenschaft der EDMX-Datei fest, die in die Ausgabeassembly eingebettet werden soll, sodass die generierten Metadatendateien in die Assembly eingebettet werden
-   Fügt einen Verweis auf die folgenden Assemblys hinzu: EntityFramework, System. ComponentModel. DataAnnotations und System. Data. Entity.
-   Erstellt EnumTestModel.TT-und EnumTestModel.Context.tt-Dateien und fügt Sie der EDMX-Datei hinzu. Diese T4-Vorlagen Dateien generieren den Code, der den von dbcontext abgeleiteten Typ und poco-Typen definiert, die den Entitäten im edmx-Modell zugeordnet werden.

## <a name="add-a-new-entity-type"></a>Neuen Entitätstyp hinzufügen

1.  Klicken Sie mit der rechten Maustaste auf einen leeren Bereich der Entwurfs Oberfläche, und wählen Sie **Add- &gt; Entity**, das Dialogfeld neue Entität wird angezeigt.
2.  Geben Sie die **Abteilung** für den Typnamen an, und geben Sie **DepartmentID** als Schlüssel Eigenschaftsnamen an, belassen Sie den Typ als **Int32** .
3.  Klicken Sie auf **OK**
4.  Klicken Sie mit der rechten Maustaste auf die Entität, und wählen Sie **Add New- &gt; Scalar**
5.  Benennen Sie die neue Eigenschaft in **Name** um.
6.  Ändern Sie den Typ der neuen Eigenschaft in **Int32** (Standardmäßig ist die neue Eigenschaft vom Typ String), um den Typ zu ändern, öffnen Sie die Eigenschaftenfenster, und ändern Sie die Type-Eigenschaft in **Int32** .
7.  Fügen Sie eine weitere skalare Eigenschaft hinzu, und benennen Sie Sie in **Budget**um. ändern Sie den Typ in **Decimal**

## <a name="add-an-enum-type"></a>Hinzufügen eines Aufzählungs Typs

1.  Klicken Sie im Entity Framework Designer mit der rechten Maustaste auf die Eigenschaft Name, und wählen Sie in **Enumeration konvertieren** aus.

    ![In Aufzählung konvertieren](~/ef6/media/converttoenum.png)

2.  Geben Sie im Dialogfeld Enumeration **Hinzufügen** den Namen **departmentnames** für den Enumerationstypnamen ein, ändern Sie den zugrunde liegenden Typ in **Int32**, und fügen Sie dann die folgenden Member zum-Typ hinzu: Englisch, Math und Economics.

    ![Umerationstyp hinzufügen](~/ef6/media/addenumtype.png)

3.  Klicken Sie auf **OK**.
4.  Speichern Sie das Modell, und erstellen Sie das Projekt.
    > [!NOTE]
    > Wenn Sie erstellen, werden möglicherweise Warnungen zu nicht zugeordneten Entitäten und Zuordnungen in der Fehlerliste angezeigt. Sie können diese Warnungen ignorieren, da die Fehler nach dem Generieren der Datenbank aus dem Modell entfernt werden.

Wenn Sie die Eigenschaftenfenster betrachten, werden Sie feststellen, dass der Typ der Name-Eigenschaft in **departmentnames** geändert wurde und der neu hinzugefügte Enumerationstyp der Liste der Typen hinzugefügt wurde.

Wenn Sie zum Fenstermodell Browser wechseln, sehen Sie, dass der Typ auch dem Knoten Enumerationstypen hinzugefügt wurde.

![Modell Browser](~/ef6/media/modelbrowser.png)

>[!NOTE]
> Sie können in diesem Fenster auch neue Enumerationstypen hinzufügen, indem Sie auf die Rechte Maustaste klicken und **Enumerationstyp hinzufügen**auswählen. Nachdem der Typ erstellt wurde, wird er in der Liste der Typen angezeigt, und Sie können einer Eigenschaft zuordnen.

## <a name="generate-database-from-model"></a>Datenbank aus Modell generieren

Nun können wir eine Datenbank generieren, die auf dem Modell basiert.

1.  Klicken Sie mit der rechten Maustaste auf einen leeren Bereich auf der Entity Designer Oberfläche, und wählen Sie **Datenbank aus Modell generieren** .
2.  Das Dialog Feld Wählen Sie Ihre Datenverbindung aus des Assistenten zum Generieren von Datenbanken wird angezeigt. Klicken Sie auf die Schaltfläche **neue Verbindung** **, und geben** Sie für den Servernamen **(localdb) \\ mssqllocaldb** an, und klicken Sie auf **OK** .
3.  Wenn Sie in einem Dialogfeld gefragt werden, ob Sie eine neue Datenbank erstellen möchten, klicken Sie auf **Ja**.
4.  Klicken Sie auf **weiter** , und der Assistent zum Erstellen einer Datenbank generiert die Datendefinitionssprache (DDL) zum Erstellen einer Datenbank. die generierte DDL wird im Dialog Feld Zusammenfassung und Einstellungen angezeigt. Hinweis: die DDL enthält keine Definition für eine Tabelle, die dem Enumerationstyp zugeordnet ist.
5.  Klicken Sie auf **Fertig** stellen klicken Sie das DDL-Skript nicht aus.
6.  Der Assistent zum Erstellen einer Datenbank führt Folgendes aus: öffnet die Datei " **enumtest. edmx. SQL** " im T-SQL-Editor, generiert die Speicher Schema-und Zuordnungsabschnitte der EDMX-Datei und fügt der App.config
7.  Klicken Sie im T-SQL-Editor auf die Rechte Maustaste, und wählen Sie das Dialogfeld Verbindung mit Server herstellen aus, und **Geben Sie** die **Connect** Verbindungsinformationen aus Schritt 2 ein.
8.  Um das generierte Schema anzuzeigen, klicken Sie in SQL Server-Objekt-Explorer mit der rechten Maustaste auf den Datenbanknamen, und wählen Sie **Aktualisieren** .

## <a name="persist-and-retrieve-data"></a>Persistenz und Abrufen von Daten

Öffnen Sie die Datei Program.cs, in der die Main-Methode definiert ist. Fügen Sie der Main-Funktion den folgenden Code hinzu. Der Code fügt dem Kontext ein neues Abteilungs Objekt hinzu. Anschließend werden die Daten gespeichert. Der Code führt außerdem eine LINQ-Abfrage aus, die eine Abteilung zurückgibt, in der der Name departmentnames. English lautet.

``` csharp
using (var context = new EnumTestModelContainer())
{
    context.Departments.Add(new Department{ Name = DepartmentNames.English });

    context.SaveChanges();

    var department = (from d in context.Departments
                        where d.Name == DepartmentNames.English
                        select d).FirstOrDefault();

    Console.WriteLine(
        "DepartmentID: {0} and Name: {1}",
        department.DepartmentID,  
        department.Name);
}
```

Kompilieren Sie die Anwendung, und führen Sie sie aus. Das Programm erzeugt die folgende Ausgabe:

```console
DepartmentID: 1 Name: English
```

Zum Anzeigen von Daten in der Datenbank klicken Sie in SQL Server-Objekt-Explorer mit der rechten Maustaste auf den Datenbanknamen, und wählen Sie **Aktualisieren**aus. Klicken Sie dann auf die Rechte Maustaste in der Tabelle, und wählen Sie **Daten anzeigen**aus.

## <a name="summary"></a>Zusammenfassung

In dieser exemplarischen Vorgehensweise wurde erläutert, wie Enumerationstypen mithilfe der Entity Framework Designer zugeordnet werden und wie Enumerationen im Code verwendet werden. 
