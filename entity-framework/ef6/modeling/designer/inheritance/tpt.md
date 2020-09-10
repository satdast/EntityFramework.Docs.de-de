---
title: Designer-TPT-Vererbung-EF6
description: Designer-TPT-Vererbung in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: efc78c31-b4ea-4ea3-a0cd-c69eb507020e
uid: ef6/modeling/designer/inheritance/tpt
ms.openlocfilehash: cb686a9f51b14651c210102845de495906318747
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620470"
---
# <a name="designer-tpt-inheritance"></a>Designer-TPT-Vererbung
In dieser schrittweisen exemplarischen Vorgehensweise wird veranschaulicht, wie Sie die "Tabelle pro Typ"-Vererbung (TPT) in Ihrem Modell mithilfe der Entity Framework Designer (EF-Designer) implementieren. Bei der "Tabelle pro Typ"-Vererbung wird eine separate Tabelle in der Datenbank verwendet, um die Daten für nicht geerbte Eigenschaften und Schlüsseleigenschaften für jeden Typ in der Vererbungshierarchie zu verwalten.

In dieser exemplarischen Vorgehensweise ordnen wir die Entitäten **Course** (Basistyp), **OnlineCourse** (abgeleitet von Course) und **OnsiteCourse**   (abgeleitet von **Course**) auf Tabellen mit denselben Namen zu. Wir erstellen ein Modell aus der Datenbank und ändern dann das Modell, um die TPT-Vererbung zu implementieren.

Sie können auch mit dem Model First beginnen und dann die Datenbank aus dem Modell generieren. Der EF-Designer verwendet standardmäßig die TPT-Strategie, sodass jede Vererbung im Modell separaten Tabellen zugeordnet wird.

## <a name="other-inheritance-options"></a>Andere Vererbungs Optionen

Tabelle pro Hierarchie (TPH) ist eine andere Art von Vererbung, bei der eine Datenbanktabelle verwendet wird, um Daten für alle Entitäts Typen in einer Vererbungs Hierarchie zu verwalten.Informationen zum Zuordnen der "Tabelle pro Hierarchie"-Vererbung mit dem Entity Designer finden Sie unter [EF-Designer-TPH-Vererbung](xref:ef6/modeling/designer/inheritance/tph). 

Beachten Sie, dass die Tabelle pro konkrete Typvererbung (TPC) und gemischte Vererbungs Modelle von der Entity Framework Runtime unterstützt werden, jedoch nicht vom EF-Designer unterstützt werden. Wenn Sie TPC oder gemischte Vererbung verwenden möchten, haben Sie zwei Möglichkeiten: Verwenden Sie Code First, oder bearbeiten Sie die EDMX-Datei manuell. Wenn Sie sich für die Arbeit mit der EDMX-Datei entscheiden, wird das Fenster Mappingdetails in den "abgesicherten Modus" versetzt, und Sie können den Designer nicht verwenden, um die Zuordnungen zu ändern.

## <a name="prerequisites"></a>Voraussetzungen

Um die exemplarische Vorgehensweise nachzuvollziehen, benötigen Sie Folgendes:

- Eine aktuelle Version von Visual Studio.
- Die [Beispieldatenbank "School](xref:ef6/resources/school-database)".

## <a name="set-up-the-project"></a>Einrichten des Projekts

-   Öffnen Sie Visual Studio 2012.
-   Wählen Sie **Datei- &gt; neu- &gt; Projekt** aus.
-   Klicken Sie im linken Bereich auf **Visual C \# **, und wählen Sie dann die **Konsolen** Vorlage aus.
-   Geben Sie **tptdbfirstsample**   als Name ein.
-   Wählen Sie **OK** aus.

## <a name="create-a-model"></a>Modellerstellung

-   Klicken Sie in Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie **Add- &gt; New Item**aus.
-   Wählen Sie im linken Menü **Daten** aus, und wählen Sie dann im Bereich Vorlagen die Option **ADO.NET Entity Data Model** aus.
-   Geben Sie als Dateiname **tptmodel. edmx** ein, und klicken Sie dann auf **Hinzufügen**.
-   Wählen Sie im Dialogfeld Modell Inhalte auswählen die Option**   aus Datenbank generieren aus**, und klicken Sie dann auf **weiter**.
-   Klicken Sie auf **neue Verbindung**.
    Geben Sie im Dialogfeld Verbindungs Eigenschaften den Servernamen ein (z. b. **(localdb) \\ mssqllocaldb**), wählen Sie die Authentifizierungsmethode aus, geben Sie **School**   als Datenbanknamen School ein, und klicken Sie dann auf **OK**.
    Das Dialogfeld Wählen Sie Ihre Datenverbindung aus wird mit Ihrer Daten bankverbindungs Einstellung aktualisiert.
-   Wählen Sie im Dialogfeld Datenbankobjekte auswählen unter dem Knoten Tabellen die Tabellen " **Department**", " **Course", "OnlineCourse" und "OnsiteCourse** " aus.
-   Klicken Sie auf **Finish**.

Die Entity Designer, die eine Entwurfs Oberfläche zum Bearbeiten des Modells bereitstellt, wird angezeigt. Alle Objekte, die Sie im Dialogfeld Wählen Sie Ihre Datenbankobjekte ausgewählt haben, werden dem Modell hinzugefügt.

## <a name="implement-table-per-type-inheritance"></a>"Tabelle pro Typ"-Vererbung implementieren

-   Klicken Sie auf der Entwurfs Oberfläche mit der rechten Maustaste auf den Entitätstyp **OnlineCourse** , und wählen Sie **Eigenschaften**aus.
-   Legen Sie im Fenster **Eigenschaften** die Eigenschaft Basistyp auf **Kurs**fest.
-   Klicken Sie mit der rechten Maustaste auf den Entitätstyp **OnsiteCourse** und wählen Sie **Eigenschaften**.
-   Legen Sie im Fenster **Eigenschaften** die Eigenschaft Basistyp auf **Kurs**fest.
-   Klicken Sie mit der rechten Maustaste auf die Zuordnung (die Linie) zwischen den Entitäts Typen **OnlineCourse** und **Course** .
    Wählen Sie **aus Modell löschen aus**.
-   Klicken Sie mit der rechten Maustaste auf die Zuordnung zwischen den Entitäts Typen **OnsiteCourse** und **Course** .
    Wählen Sie **aus Modell löschen aus**.

Wir löschen jetzt die **CourseID** -Eigenschaft aus " **OnlineCourse** " und " **OnsiteCourse** ", da diese Klassen **CourseID** vom **Course** -Basistyp erben.

-   Klicken Sie mit der rechten Maustaste auf die Eigenschaft **CourseID** des Entitäts Typs **OnlineCourse** , und wählen Sie dann **aus Modell löschen aus**.
-   Klicken Sie mit der rechten Maustaste auf die Eigenschaft **CourseID** des Entitäts Typs **OnsiteCourse** , und wählen Sie dann **aus Modell löschen aus** .
-   Die "Tabelle pro Typ"-Vererbung ist damit implementiert.

![Tabelle pro Typ](~/ef6/media/tpt.png)

## <a name="use-the-model"></a>Verwenden des Modells

Öffnen Sie die Datei **Program.cs** , in der die **Main** -Methode definiert ist. Fügen Sie den folgenden Code in die **Main** -Funktion ein. Der Code führt drei Abfragen aus. Die erste Abfrage führt alle **Kurse** zurück, die mit der angegebenen Abteilung verknüpft sind. In der zweiten Abfrage wird die **OfType** -Methode verwendet, um **OnlineCourses** zurückzugeben, die mit der angegebenen Abteilung verknüpft sind. Die dritte Abfrage gibt " **OnsiteCourses**" zurück.

``` csharp
    using (var context = new SchoolEntities())
    {
        foreach (var department in context.Departments)
        {
            Console.WriteLine("The {0} department has the following courses:",
                               department.Name);

            Console.WriteLine("   All courses");
            foreach (var course in department.Courses )
            {
                Console.WriteLine("     {0}", course.Title);
            }

            foreach (var course in department.Courses.
                OfType<OnlineCourse>())
            {
                Console.WriteLine("   Online - {0}", course.Title);
            }

            foreach (var course in department.Courses.
                OfType<OnsiteCourse>())
            {
                Console.WriteLine("   Onsite - {0}", course.Title);
            }
        }
    }
```
