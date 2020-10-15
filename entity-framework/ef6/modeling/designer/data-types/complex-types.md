---
title: 'Komplexe Typen: EF-Designer-EF6'
description: 'Komplexe Typen: EF-Designer in Entity Framework 6'
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/data-types/complex-types
ms.openlocfilehash: abacbdd375c2871e6c4205e0084764029b149a40
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065042"
---
# <a name="complex-types---ef-designer"></a>Komplexe Typen: EF-Designer
In diesem Thema wird gezeigt, wie komplexe Typen mit dem Entity Framework Designer (EF-Designer) zugeordnet werden und wie Entitäten abgefragt werden, die Eigenschaften des komplexen Typs enthalten.

Die folgende Abbildung zeigt die Hauptfenster, die bei der Arbeit mit dem EF-Designer verwendet werden.

![EF-Designer](~/ef6/media/efdesigner.png)

> [!NOTE]
> Beim Erstellen des konzeptionellen Modells können Warnungen zu nicht zugeordneten Entitäten und Zuordnungen in der Fehlerliste angezeigt werden. Sie können diese Warnungen ignorieren, da die Fehler nach dem Generieren der Datenbank aus dem Modell entfernt werden.

## <a name="what-is-a-complex-type"></a>Was ist ein komplexer Typ?

Komplexe Typen sind nicht skalare Eigenschaften von Entitätstypen, mit deren Hilfe skalare Eigenschaften in Entitäten organisiert werden können. Wie Entitäten bestehen komplexe Typen aus skalaren Eigenschaften oder anderen Eigenschaften von komplexem Typ.

Beachten Sie Folgendes, wenn Sie mit Objekten arbeiten, die komplexe Typen darstellen:

-   Komplexe Typen verfügen über keine Schlüssel und können daher nicht unabhängig vorhanden sein. Komplexe Typen können nur Eigenschaften von Entitätstypen oder anderen komplexen Typen sein.
-   Komplexe Typen können nicht an Zuordnungen teilnehmen und keine Navigations Eigenschaften enthalten.
-   Eigenschaften komplexer Typen dürfen nicht **null**sein. Eine **InvalidOperationException **tritt auf, wenn **dbcontext. SaveChanges**   aufgerufen wird und ein komplexes NULL-Objekt gefunden wird. Skalare Eigenschaften komplexer Objekte können **null**sein.
-   Komplexe Typen können nicht von anderen komplexen Typen erben.
-   Sie müssen den komplexen Typ als **Klasse**definieren. 
-   EF erkennt Änderungen an Membern für ein Objekt komplexer Typen, wenn **dbcontext. DetectChanges** aufgerufen wird. Entity Framework ruft **DetectChanges** automatisch auf, wenn die folgenden Member aufgerufen werden: **dbset. Find**, **dbset. local**, **dbset. Remove**, **dbset. Add**, **dbset. Attach**, **dbcontext. SaveChanges**, **dbcontext. getvalidationerrors**, **dbcontext. Entry**, **dbchangetracker. Entries**.

## <a name="refactor-an-entitys-properties-into-new-complex-type"></a>Umgestalten der Eigenschaften einer Entität in einen neuen komplexen Typ

Wenn Sie bereits über eine Entität im konzeptionellen Modell verfügen, können Sie einige der Eigenschaften in eine komplexe Typeigenschaft umgestalten.

Wählen Sie auf der Designer Oberfläche eine oder mehrere Eigenschaften (mit Ausnahme der Navigations Eigenschaften) einer Entität aus, klicken Sie dann mit der rechten Maustaste, und wählen Sie **umgestalten- &gt; in neuen komplexen Typ verschieben**aus.

![Umgestalten in neuen komplexen Typ](~/ef6/media/refactor.png)

Ein neuer komplexer Typ mit den ausgewählten Eigenschaften wird dem **Modell Browser**hinzugefügt. Dem komplexen Typ wird ein Standardname zugewiesen.

Die ausgewählten Eigenschaften werden durch eine komplexe Eigenschaft des neu erstellten Typs ersetzt. Alle Eigenschaftenzuordnungen werden beibehalten.

![Umgestalten in den neuen komplexen Typ 2](~/ef6/media/refactor2.png)

## <a name="create-a-new-complex-type"></a>Neuen komplexen Typ erstellen

Sie können auch einen neuen komplexen Typ erstellen, der keine Eigenschaften einer vorhandenen Entität enthält.

Klicken Sie im Modell Browser mit der rechten Maustaste auf den Ordner **Complex Types** , und zeigen Sie auf **AddNew Complex Type...**. Alternativ können Sie den Ordner **komplexe Typen** auswählen und die **Einfügetaste**   auf der Tastatur drücken.

![Neuen komplexen Typ hinzufügen](~/ef6/media/addnewcomplextype.png)

Dem Ordner wird ein neuer komplexer Typ mit einem Standardnamen hinzugefügt. Sie können dem Typ jetzt Eigenschaften hinzufügen.

## <a name="add-properties-to-a-complex-type"></a>Hinzufügen von Eigenschaften zu einem komplexen Typ

Bei den Eigenschaften eines komplexen Typs kann es sich um skalare Typen oder vorhandene komplexe Typen handeln. Für die Eigenschaften eines komplexen Typs können jedoch keine Zirkelverweise verwendet werden. Beispielsweise kann ein komplexer Typ " **onsitecourtendetails**"   keine Eigenschaft des komplexen Typs " **onsitecourtendetails**" aufweisen.

Einem komplexen Typ können anhand der unten aufgeführten Methoden Eigenschaften hinzugefügt werden.

-   Klicken Sie im Modell Browser mit der rechten Maustaste auf einen komplexen Typ, zeigen Sie auf **Hinzufügen**, zeigen Sie auf **skalare Eigenschaft**   oder **komplexe Eigenschaft**, und wählen Sie dann den gewünschten Eigenschaftentyp aus. Alternativ können Sie einen komplexen Typ auswählen und dann die **Einfügetaste**   auf der Tastatur drücken.  

    ![Hinzufügen von Eigenschaften zu einem komplexen Typ](~/ef6/media/addpropertiestocomplextype.png)

    Dem komplexen Typ wird eine neue Eigenschaft mit einem Standardnamen hinzugefügt.

- - ODER -

-   Klicken Sie mit der rechten Maustaste auf eine Entitäts Eigenschaft auf der **EF-Designer** -Oberfläche, und wählen Sie **Kopieren** **aus. Klicken Sie dann**mit der rechten Maustaste auf den komplexen Typ im **Modell Browser**

## <a name="rename-a-complex-type"></a>Umbenennen eines komplexen Typs

Wenn Sie einen komplexen Typ umbenennen, werden alle Verweise auf den Typ im gesamten Projekt aktualisiert.

-   Doppelklicken Sie im **Modell Browser**langsam auf einen komplexen Typ.
    Der Name wird markiert und kann bearbeitet werden.

- - ODER -

-   Klicken Sie im **Modell Browser** mit der rechten Maustaste auf einen komplexen Typ, und wählen Sie **Umbenennen**.

- - ODER -

-   Markieren Sie im Modellbrowser einen komplexen Typ, und drücken Sie die F2-TASTE.

- - ODER -

-   Klicken Sie im **Modell Browser** mit der rechten Maustaste auf einen komplexen Typ, und wählen Sie **Eigenschaften**aus. Bearbeiten Sie den Namen im **Eigenschaften**   Fenster.

## <a name="add-an-existing-complex-type-to-an-entity-and-map-its-properties-to-table-columns"></a>Fügen Sie einer Entität einen vorhandenen komplexen Typ hinzu, und ordnen Sie die zugehörigen Eigenschaften Tabellen Spalten zu.

1.  Klicken Sie mit der rechten Maustaste auf eine Entität, zeigen Sie auf **Neu hinzufügen**, und wählen Sie **komplexe Eigenschaft**
    Der Entität wird eine Eigenschaft eines komplexen Typs mit einem Standardnamen hinzugefügt. Der Eigenschaft wird ein Standardtyp zugewiesen (ausgewählt aus den vorhandenen komplexen Typen).
2.  Weisen Sie der-Eigenschaft im **Eigenschaften**Fenster den gewünschten Typ zu   .
    Nachdem Sie einer Entität eine Eigenschaft eines komplexen Typs hinzugefügt haben, müssen Sie die zugehörigen Eigenschaften Tabellenspalten zuordnen.
3.  Klicken Sie in der Entwurfs Oberfläche oder im **Modell Browser**mit der rechten Maustaste auf einen Entitätstyp,   und wählen Sie **Tabellen**Zuordnungen aus.
    Die Tabellen Zuordnungen werden im Fenster **Mappingdetails**angezeigt   .
4.  Erweitern Sie den Knoten **maps to &lt; Table Name &gt; **   .
    Ein Knoten **Spalten**Zuordnungen wird   angezeigt.
5.  Erweitern Sie den Knoten **Spalten**Zuordnungen   .
    Eine Liste aller Spalten in der Tabelle wird angezeigt. Die Standardeigenschaften (sofern vorhanden), denen die Spalten zugeordnet sind, werden unter der Überschrift **Wert/Eigenschaft**aufgelistet   .
6.  Wählen Sie die Spalte aus, die Sie zuordnen möchten, und klicken Sie dann mit der rechten Maustaste auf das entsprechende Feld **Wert/Eigenschaft**   .
    Eine Dropdownliste aller skalaren Eigenschaften wird angezeigt.
7.  Wählen Sie die entsprechende Eigenschaft aus.

    ![Komplexer Kartentyp](~/ef6/media/mapcomplextype.png)

8.  Wiederholen Sie die Schritte 6 und 7 für jede Tabellenspalte.

>[!NOTE]
> Um eine Spalten Zuordnung zu löschen, wählen Sie die Spalte aus, die Sie zuordnen möchten, und klicken Sie dann auf das Feld **Wert/Eigenschaft**   . Wählen Sie dann in der Dropdown Liste die Option **Löschen** aus.

## <a name="map-a-function-import-to-a-complex-type"></a>Zuordnen eines Funktions Imports zu einem komplexen Typ

Funktionsimporte basieren auf gespeicherten Prozeduren. Um einen Funktionsimport einem komplexen Typ zuzuordnen, muss die Zahl der von der entsprechenden gespeicherten Prozedur zurückgegebenen Spalten der Zahl der Eigenschaften des komplexen Typs entsprechen, und die Spalten müssen einen Speichertyp aufweisen, der mit den Typen der Eigenschaften kompatibel ist.

-   Doppelklicken Sie auf eine importierte Funktion, die einem komplexen Typ zugeordnet werden soll.

    ![Funktions Importe](~/ef6/media/functionimports.png)

-   Geben Sie wie folgt die Einstellungen für den neuen Funktionsimport ein:
    -   Geben Sie im Feld **Name der gespeicherten Prozedur**die gespeicherte Prozedur an, für die Sie einen Funktions Import erstellen   . Dieses Feld wird als Dropdownliste angezeigt, die alle gespeicherten Prozeduren im Speichermodell enthält.
    -   Geben Sie den Namen des Funktions Imports im Feld **Funktions Import Name**an   .
    -   Wählen Sie **Komplex**   als Rückgabetyp aus, und geben Sie dann den spezifischen komplexen Rückgabetyp an, indem Sie den entsprechenden Typ aus der Dropdown Liste auswählen.

        ![Funktions Import bearbeiten](~/ef6/media/editfunctionimport.png)

-   Klicken Sie auf **OK**.
    Der Funktionsimport-Eintrag wird im konzeptionellen Modell erstellt.

### <a name="customize-column-mapping-for-function-import"></a>Anpassen der Spalten Zuordnung für den Funktions Import

-   Klicken Sie im Modell Browser mit der rechten Maustaste auf den Funktions Import, und wählen Sie **Funktions Import Zuordnung**aus.
    Das Fenster **Mappingdetails**wird   angezeigt und zeigt die Standard Zuordnung für den Funktions Import an. Pfeilsymbole geben die Zuordnungen der Spaltenwerte zu den Eigenschaftswerten an. Standardmäßig wird angenommen, dass die Spaltennamen mit den Namen der Eigenschaften des komplexen Typs identisch sind. Die Standardspaltennamen werden in grauem Text angezeigt.
-   Ändern Sie, falls erforderlich, die Spaltennamen so, dass sie mit den Spaltennamen übereinstimmen, die von der gespeicherten Prozedur zurückgegeben werden, die dem Funktionsimport entspricht.

## <a name="delete-a-complex-type"></a>Löschen eines komplexen Typs

Wenn Sie einen komplexen Typ löschen, wird der Typ aus dem konzeptionellen Modell gelöscht, und die Mappings für alle Instanzen des Typs werden gelöscht. Verweise auf den Typ werden jedoch nicht aktualisiert. Wenn eine Entität z. b. über eine komplexe Typeigenschaft vom Typ ComplexType1 und ComplexType1 im **Modell Browser**gelöscht wird, wird die zugehörige Entitäts Eigenschaft nicht aktualisiert. Das Modell wird nicht überprüft, weil es eine Entität enthält, die auf einen gelöschten komplexen Typ verweist. Verweise auf gelöschte komplexe Typen können mit dem Entity Designer aktualisiert werden.

-   Klicken Sie im Modell Browser mit der rechten Maustaste auf einen komplexen Typ, und wählen Sie **Löschen**aus.

- - ODER -

-   Markieren Sie im Modellbrowser einen komplexen Typ, und drücken Sie dann die ENTF-TASTE auf der Tastatur.

## <a name="query-for-entities-containing-properties-of-complex-type"></a>Abfragen von Entitäten mit Eigenschaften des komplexen Typs

Der folgende Code zeigt, wie eine Abfrage ausgeführt wird, die eine Auflistung von Entitätstyp Objekten zurückgibt, die eine Eigenschaft eines komplexen Typs enthalten.

``` csharp
    using (SchoolEntities context = new SchoolEntities())
    {
        var courses =
            from c in context.OnsiteCourses
            order by c.Details.Time
            select c;

        foreach (var c in courses)
        {
            Console.WriteLine("Time: " + c.Details.Time);
            Console.WriteLine("Days: " + c.Details.Days);
            Console.WriteLine("Location: " + c.Details.Location);
        }
    }
```
