---
title: Beziehungen-EF-Designer-EF6
description: 'Beziehungen: EF-Designer in Entity Framework 6'
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/relationships
ms.openlocfilehash: 65960dcfcafc239b5ecd8b396a52dababbb4ab08
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073291"
---
# <a name="relationships---ef-designer"></a>Beziehungen: EF-Designer
> [!NOTE]
> Diese Seite enthält Informationen zum Einrichten von Beziehungen in Ihrem Modell mit dem EF-Designer. Allgemeine Informationen zu Beziehungen in EF und zum Zugreifen auf und Bearbeiten von Daten mithilfe von Beziehungen finden Sie unter [Beziehungen & Navigations Eigenschaften](xref:ef6/fundamentals/relationships).

Zuordnungen definieren Beziehungen zwischen Entitäts Typen in einem Modell. In diesem Thema wird gezeigt, wie Zuordnungen mit dem Entity Framework Designer (EF-Designer) zugeordnet werden. Die folgende Abbildung zeigt die Hauptfenster, die bei der Arbeit mit dem EF-Designer verwendet werden.

![EF-Designer](~/ef6/media/efdesigner.png)

> [!NOTE]
> Beim Erstellen des konzeptionellen Modells können Warnungen zu nicht zugeordneten Entitäten und Zuordnungen in der Fehlerliste angezeigt werden. Sie können diese Warnungen ignorieren, da die Fehler nach dem Generieren der Datenbank aus dem Modell entfernt werden.

## <a name="associations-overview"></a>Übersicht über Zuordnungen

Wenn Sie das Modell mit dem EF-Designer entwerfen, stellt eine EDMX-Datei das Modell dar. In der EDMX-Datei definiert ein **Association** -Element eine Beziehung zwischen zwei Entitäts Typen. Eine Zuordnung muss die Entitätstypen, die in der Beziehung enthalten sind, und die mögliche Anzahl von Entitätstypen an den Enden der Beziehung angeben, die auch als Multiplizität bezeichnet wird. Die Multiplizität eines Zuordnungs Endes kann über einen Wert von eins (1), NULL oder eins (0.. 1) oder viele ( \* ) verfügen. Diese Informationen werden in zwei untergeordneten **Endelementen** angegeben.

Zur Laufzeit kann auf Entitätstyp Instanzen an einem Ende einer Zuordnung über Navigations Eigenschaften oder Fremdschlüssel zugegriffen werden (wenn Sie Fremdschlüssel in ihren Entitäten verfügbar machen). Wenn Fremdschlüssel verfügbar gemacht werden, wird die Beziehung zwischen den Entitäten mit einem **referentialeinschränkung** -Element verwaltet (ein untergeordnetes Element des **Association** -Elements). Es wird empfohlen, Fremdschlüssel für Beziehungen in ihren Entitäten immer verfügbar zu machen.

> [!NOTE]
> In der m:n-Zahl ( \* : \* ) können den Entitäten keine Fremdschlüssel hinzugefügt werden. In einer \* :- \* Beziehung werden die Zuordnungs Informationen mit einem unabhängigen Objekt verwaltet.

Weitere Informationen zu CSDL-Elementen (**referentialeinschränkung**, **Association**usw.) finden Sie in der [CSDL-Spezifikation](xref:ef6/modeling/designer/advanced/edmx/csdl-spec).

## <a name="create-and-delete-associations"></a>Erstellen und Löschen von Zuordnungen

Durch das Erstellen einer Verknüpfung mit dem EF-Designer wird der Modell Inhalt der EDMX-Datei aktualisiert. Nachdem Sie eine Zuordnung erstellt haben, müssen Sie die Zuordnungen für die Zuordnung erstellen (siehe weiter unten in diesem Thema).

> [!NOTE]
> In diesem Abschnitt wird davon ausgegangen, dass Sie bereits die Entitäten hinzugefügt haben, zwischen denen Sie eine Zuordnung zu Ihrem Modell erstellen möchten.

### <a name="to-create-an-association"></a>So erstellen Sie eine Zuordnung

1.  Klicken Sie mit der rechten Maustaste auf einen leeren Bereich der Entwurfs Oberfläche, zeigen Sie auf **Neu hinzufügen**, und wählen Sie Zuordnung **...** aus.
2.  Geben Sie im Dialogfeld Zuordnung **Hinzufügen** die Einstellungen für die Zuordnung ein.

    ![Zuordnung hinzufügen](~/ef6/media/addassociation.png)

    > [!NOTE]
    > Sie können auswählen, dass den Entitäten an den Enden der Zuordnung keine Navigations Eigenschaften oder Fremdschlüssel Eigenschaften hinzugefügt werden sollen, indem Sie die **Navigations Eigenschaft **löschen und den Entitäts- ** &lt; Typnamen &gt; Entitäts-Kontrollkästchen Fremdschlüssel Eigenschaften hinzufügen **. Wird nur eine Navigationseigenschaft hinzugefügt, kann die Zuordnung nur in einer Richtung traversiert werden. Falls Sie keine Navigationseigenschaften hinzufügen, müssen Sie Fremdschlüsseleigenschaften hinzufügen, um auf Entitäten an den Enden der Zuordnung zuzugreifen.
    
3.  Klicken Sie auf **OK**.

### <a name="to-delete-an-association"></a>So löschen Sie eine Zuordnung

Um eine Zuordnung zu löschen, führen Sie einen der folgenden Schritte aus:

-   Klicken Sie mit der rechten Maustaste auf die Verknüpfung auf der EF-Designer Oberfläche, und wählen Sie **Löschen**

- - ODER -

-   Wählen Sie eine oder mehrere Zuordnungen aus, und drücken Sie die ENTF-Taste.

## <a name="include-foreign-key-properties-in-your-entities-referential-constraints"></a>Einschließen von Fremdschlüssel Eigenschaften in Ihre Entitäten (Referenzielle Einschränkungen)

Es wird empfohlen, Fremdschlüssel für Beziehungen in ihren Entitäten immer verfügbar zu machen. Entity Framework verwendet eine referenzielle Einschränkung, um zu identifizieren, dass eine Eigenschaft als Fremdschlüssel für eine Beziehung fungiert.

Wenn Sie beim Erstellen einer Beziehung das Kontrollkästchen ***Fremdschlüssel Eigenschaften der &lt; &gt; Entität Entitäts Typen Name hinzufügen*** aktiviert haben, wurde diese referenzielle Einschränkung für Sie hinzugefügt.

Wenn Sie den EF-Designer verwenden, um eine referenzielle Einschränkung hinzuzufügen oder zu bearbeiten, fügt der EF-Designer ein **referentialeinschränkung**-   Element im CSDL-Inhalt der EDMX-Datei hinzu oder ändert es.

-   Doppelklicken Sie auf die Zuordnung, die Sie bearbeiten möchten.
    Das Dialogfeld **referenzielle Einschränkung**wird   angezeigt.
-    **Principal**   Wählen Sie in der Dropdown Liste Prinzipal die Prinzipal Entität in der referenziellen Einschränkung aus.
    Die Schlüsseleigenschaften der Entität werden der Liste **Prinzipal Schlüssel**   im Dialogfeld hinzugefügt.
-    **Dependent**   Wählen Sie in der Dropdown Liste abhängig die abhängige Entität in der referenziellen Einschränkung aus.
-   Wählen Sie für jeden Prinzipal Schlüssel, der über einen abhängigen Schlüssel verfügt, einen entsprechenden abhängigen Schlüssel aus den Dropdown Listen in der Spalte **abhängige Schlüssel**aus   .

    ![Ref-Einschränkung](~/ef6/media/refconstraint.png)

-   Klicken Sie auf **OK**.

## <a name="create-and-edit-association-mappings"></a>Zuordnungs Zuordnungen erstellen und bearbeiten

Im Fenster **Mappingdetails**des EF-Designers können Sie angeben, wie eine Zuordnung der Datenbank zugeordnet werden soll   .

> [!NOTE]
> Sie können nur Details für die Zuordnungen zuordnen, für die keine referenzielle Einschränkung festgelegt ist. Wenn eine referenzielle Einschränkung angegeben wird, ist eine Fremdschlüssel Eigenschaft in der Entität enthalten, und Sie können die Zuordnungs Details für die Entität verwenden, um die Spalte zu steuern, der der Fremdschlüssel zugeordnet ist.

### <a name="create-an-association-mapping"></a>Erstellen einer Zuordnungs Zuordnung

-   Klicken Sie in der Entwurfs Oberfläche mit der rechten Maustaste auf eine Zuordnung, und wählen Sie **Tabellen Zuordnung**.
    Dadurch wird die Zuordnungs Zuordnung im Fenster **Mappingdetails**angezeigt   .
-   Klicken Sie auf **Tabelle oder Sicht hinzufügen**.
    Eine Dropdownliste wird angezeigt, die die Tabellen im Speichermodell enthält.
-   Wählen Sie die Tabelle aus, der die Zuordnung zugeordnet wird.
    Im Fenster **Mappingdetails**werden   beide Enden der Zuordnung und die Schlüsseleigenschaften für den Entitätstyp an jedem **Ende**angezeigt.
-   Klicken Sie für jede Schlüsseleigenschaft auf das Feld **Spalte**   , und wählen Sie die Spalte aus, der die Eigenschaft zugeordnet werden soll.

    ![Mappingdetails 4](~/ef6/media/mappingdetails4.png)

### <a name="edit-an-association-mapping"></a>Bearbeiten einer Zuordnungs Zuordnung

-   Klicken Sie in der Entwurfs Oberfläche mit der rechten Maustaste auf eine Zuordnung, und wählen Sie **Tabellen Zuordnung**.
    Dadurch wird die Zuordnungs Zuordnung im Fenster **Mappingdetails**angezeigt   .
-   Klicken Sie **auf dem &lt; Tabellen &gt; Namen auf Maps**.
    Eine Dropdownliste wird angezeigt, die die Tabellen im Speichermodell enthält.
-   Wählen Sie die Tabelle aus, der die Zuordnung zugeordnet wird.
    Im Fenster **Mappingdetails**werden   beide Enden der Zuordnung und die Schlüsseleigenschaften für den Entitätstyp an jedem Ende angezeigt.
-   Klicken Sie für jede Schlüsseleigenschaft auf das Feld **Spalte**   , und wählen Sie die Spalte aus, der die Eigenschaft zugeordnet werden soll.

## <a name="edit-and-delete-navigation-properties"></a>Bearbeiten und Löschen von Navigations Eigenschaften

Navigations Eigenschaften sind Verknüpfungs Eigenschaften, die verwendet werden, um die Entitäten an den Enden einer Zuordnung in einem Modell zu suchen. Navigationseigenschaften können beim Erstellen einer Zuordnung zwischen zwei Entitätstypen erstellt werden.

#### <a name="to-edit-navigation-properties"></a>So bearbeiten Sie Navigations Eigenschaften

-   Wählen Sie auf der EF-Designer-Oberfläche eine Navigations Eigenschaft aus.
    Informationen zur Navigations Eigenschaft werden im Visual Studio- **Eigenschaften**   Fenster angezeigt.
-   Ändern Sie die Eigenschafts Einstellungen im **Eigenschaften**   Fenster.

#### <a name="to-delete-navigation-properties"></a>So löschen Sie Navigations Eigenschaften

-   Wenn Fremdschlüssel nicht für Entitätstypen im konzeptionellen Modell verfügbar gemacht werden, kann das Löschen einer Navigationseigenschaft dazu führen, dass die entsprechende Zuordnung nur in einer Richtung oder gar nicht traversiert werden kann.
-   Klicken Sie in der EF Designer-Oberfläche mit der rechten Maustaste auf eine Navigations Eigenschaft, und wählen Sie **Löschen**
