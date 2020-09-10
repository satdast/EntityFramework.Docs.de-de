---
title: Beziehungen-EF-Designer-EF6
description: 'Beziehungen: EF-Designer in Entity Framework 6'
author: divega
ms.date: 10/23/2016
ms.assetid: 402fe960-754b-470f-976b-e5de3e9986b5
uid: ef6/modeling/designer/relationships
ms.openlocfilehash: f7c801dd8f8ae81cfe44283b7575cfe869ce26d3
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620416"
---
# <a name="relationships---ef-designer"></a><span data-ttu-id="41e3e-103">Beziehungen: EF-Designer</span><span class="sxs-lookup"><span data-stu-id="41e3e-103">Relationships - EF Designer</span></span>
> [!NOTE]
> <span data-ttu-id="41e3e-104">Diese Seite enthält Informationen zum Einrichten von Beziehungen in Ihrem Modell mit dem EF-Designer.</span><span class="sxs-lookup"><span data-stu-id="41e3e-104">This page provides information about setting up relationships in your model using the EF Designer.</span></span> <span data-ttu-id="41e3e-105">Allgemeine Informationen zu Beziehungen in EF und zum Zugreifen auf und Bearbeiten von Daten mithilfe von Beziehungen finden Sie unter [Beziehungen & Navigations Eigenschaften](xref:ef6/fundamentals/relationships).</span><span class="sxs-lookup"><span data-stu-id="41e3e-105">For general information about relationships in EF and how to access and manipulate data using relationships, see [Relationships & Navigation Properties](xref:ef6/fundamentals/relationships).</span></span>

<span data-ttu-id="41e3e-106">Zuordnungen definieren Beziehungen zwischen Entitäts Typen in einem Modell.</span><span class="sxs-lookup"><span data-stu-id="41e3e-106">Associations define relationships between entity types in a model.</span></span> <span data-ttu-id="41e3e-107">In diesem Thema wird gezeigt, wie Zuordnungen mit dem Entity Framework Designer (EF-Designer) zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="41e3e-107">This topic shows how to map associations with the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="41e3e-108">Die folgende Abbildung zeigt die Hauptfenster, die bei der Arbeit mit dem EF-Designer verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="41e3e-108">The following image shows the main windows that are used when working with the EF Designer.</span></span>

![EF-Designer](~/ef6/media/efdesigner.png)

> [!NOTE]
> <span data-ttu-id="41e3e-110">Beim Erstellen des konzeptionellen Modells können Warnungen zu nicht zugeordneten Entitäten und Zuordnungen in der Fehlerliste angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="41e3e-110">When you build the conceptual model, warnings about unmapped entities and associations may appear in the Error List.</span></span> <span data-ttu-id="41e3e-111">Sie können diese Warnungen ignorieren, da die Fehler nach dem Generieren der Datenbank aus dem Modell entfernt werden.</span><span class="sxs-lookup"><span data-stu-id="41e3e-111">You can ignore these warnings because after you choose to generate the database from the model, the errors will go away.</span></span>

## <a name="associations-overview"></a><span data-ttu-id="41e3e-112">Übersicht über Zuordnungen</span><span class="sxs-lookup"><span data-stu-id="41e3e-112">Associations Overview</span></span>

<span data-ttu-id="41e3e-113">Wenn Sie das Modell mit dem EF-Designer entwerfen, stellt eine EDMX-Datei das Modell dar.</span><span class="sxs-lookup"><span data-stu-id="41e3e-113">When you design your model using the EF Designer, an .edmx file represents your model.</span></span> <span data-ttu-id="41e3e-114">In der EDMX-Datei definiert ein **Association** -Element eine Beziehung zwischen zwei Entitäts Typen.</span><span class="sxs-lookup"><span data-stu-id="41e3e-114">In the .edmx file, an **Association** element defines a relationship between two entity types.</span></span> <span data-ttu-id="41e3e-115">Eine Zuordnung muss die Entitätstypen, die in der Beziehung enthalten sind, und die mögliche Anzahl von Entitätstypen an den Enden der Beziehung angeben, die auch als Multiplizität bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="41e3e-115">An association must specify the entity types that are involved in the relationship and the possible number of entity types at each end of the relationship, which is known as the multiplicity.</span></span> <span data-ttu-id="41e3e-116">Die Multiplizität eines Zuordnungs Endes kann über einen Wert von eins (1), NULL oder eins (0.. 1) oder viele ( \* ) verfügen.</span><span class="sxs-lookup"><span data-stu-id="41e3e-116">The multiplicity of an association end can have a value of one (1), zero or one (0..1), or many (\*).</span></span> <span data-ttu-id="41e3e-117">Diese Informationen werden in zwei untergeordneten **Endelementen** angegeben.</span><span class="sxs-lookup"><span data-stu-id="41e3e-117">This information is specified in two child **End** elements.</span></span>

<span data-ttu-id="41e3e-118">Zur Laufzeit kann auf Entitätstyp Instanzen an einem Ende einer Zuordnung über Navigations Eigenschaften oder Fremdschlüssel zugegriffen werden (wenn Sie Fremdschlüssel in ihren Entitäten verfügbar machen).</span><span class="sxs-lookup"><span data-stu-id="41e3e-118">At run time, entity type instances at one end of an association can be accessed through navigation properties or foreign keys (if you choose to expose foreign keys in your entities).</span></span> <span data-ttu-id="41e3e-119">Wenn Fremdschlüssel verfügbar gemacht werden, wird die Beziehung zwischen den Entitäten mit einem **referentialeinschränkung** -Element verwaltet (ein untergeordnetes Element des **Association** -Elements).</span><span class="sxs-lookup"><span data-stu-id="41e3e-119">With foreign keys exposed, the relationship between the entities is managed with a **ReferentialConstraint** element (a child element of the **Association** element).</span></span> <span data-ttu-id="41e3e-120">Es wird empfohlen, Fremdschlüssel für Beziehungen in ihren Entitäten immer verfügbar zu machen.</span><span class="sxs-lookup"><span data-stu-id="41e3e-120">It is recommended that you always expose foreign keys for relationships in your entities.</span></span>

> [!NOTE]
> <span data-ttu-id="41e3e-121">In der m:n-Zahl ( \* : \* ) können den Entitäten keine Fremdschlüssel hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="41e3e-121">In many-to-many (\*:\*) you cannot add foreign keys to the entities.</span></span> <span data-ttu-id="41e3e-122">In einer \* :- \* Beziehung werden die Zuordnungs Informationen mit einem unabhängigen Objekt verwaltet.</span><span class="sxs-lookup"><span data-stu-id="41e3e-122">In a \*:\* relationship, the association information is managed with an independent object.</span></span>

<span data-ttu-id="41e3e-123">Weitere Informationen zu CSDL-Elementen (**referentialeinschränkung**, **Association**usw.) finden Sie in der [CSDL-Spezifikation](xref:ef6/modeling/designer/advanced/edmx/csdl-spec).</span><span class="sxs-lookup"><span data-stu-id="41e3e-123">For information about CSDL elements (**ReferentialConstraint**, **Association**, etc.) see the [CSDL specification](xref:ef6/modeling/designer/advanced/edmx/csdl-spec).</span></span>

## <a name="create-and-delete-associations"></a><span data-ttu-id="41e3e-124">Erstellen und Löschen von Zuordnungen</span><span class="sxs-lookup"><span data-stu-id="41e3e-124">Create and Delete Associations</span></span>

<span data-ttu-id="41e3e-125">Durch das Erstellen einer Verknüpfung mit dem EF-Designer wird der Modell Inhalt der EDMX-Datei aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="41e3e-125">Creating an association with the EF Designer updates the model content of the .edmx file.</span></span> <span data-ttu-id="41e3e-126">Nachdem Sie eine Zuordnung erstellt haben, müssen Sie die Zuordnungen für die Zuordnung erstellen (siehe weiter unten in diesem Thema).</span><span class="sxs-lookup"><span data-stu-id="41e3e-126">After creating an association, you must create the mappings for the association (discussed later in this topic).</span></span>

> [!NOTE]
> <span data-ttu-id="41e3e-127">In diesem Abschnitt wird davon ausgegangen, dass Sie bereits die Entitäten hinzugefügt haben, zwischen denen Sie eine Zuordnung zu Ihrem Modell erstellen möchten.</span><span class="sxs-lookup"><span data-stu-id="41e3e-127">This section assumes that you already added the entities you wish to create an association between to your model.</span></span>

### <a name="to-create-an-association"></a><span data-ttu-id="41e3e-128">So erstellen Sie eine Zuordnung</span><span class="sxs-lookup"><span data-stu-id="41e3e-128">To create an association</span></span>

1.  <span data-ttu-id="41e3e-129">Klicken Sie mit der rechten Maustaste auf einen leeren Bereich der Entwurfs Oberfläche, zeigen Sie auf **Neu hinzufügen**, und wählen Sie Zuordnung **...** aus.</span><span class="sxs-lookup"><span data-stu-id="41e3e-129">Right-click an empty area of the design surface, point to **Add New**, and select **Association…**.</span></span>
2.  <span data-ttu-id="41e3e-130">Geben Sie im Dialogfeld Zuordnung **Hinzufügen** die Einstellungen für die Zuordnung ein.</span><span class="sxs-lookup"><span data-stu-id="41e3e-130">Fill in the settings for the association in the **Add Association** dialog.</span></span>

    ![Zuordnung hinzufügen](~/ef6/media/addassociation.png)

    > [!NOTE]
    > <span data-ttu-id="41e3e-132">Sie können auswählen, dass den Entitäten an den Enden der Zuordnung keine Navigations Eigenschaften oder Fremdschlüssel Eigenschaften hinzugefügt werden sollen, indem Sie die **Navigations Eigenschaft **löschen und den Entitäts- \*\* &lt; Typnamen &gt; Entitäts-Kontrollkästchen Fremdschlüssel Eigenschaften hinzufügen \*\*.</span><span class="sxs-lookup"><span data-stu-id="41e3e-132">You can choose to not add navigation properties or foreign key properties to the entities at the ends of the association by clearing the **Navigation Property **and **Add foreign key properties to the &lt;entity type name&gt; Entity **checkboxes.</span></span> <span data-ttu-id="41e3e-133">Wird nur eine Navigationseigenschaft hinzugefügt, kann die Zuordnung nur in einer Richtung traversiert werden.</span><span class="sxs-lookup"><span data-stu-id="41e3e-133">If you add only one navigation property, the association will be traversable in only one direction.</span></span> <span data-ttu-id="41e3e-134">Falls Sie keine Navigationseigenschaften hinzufügen, müssen Sie Fremdschlüsseleigenschaften hinzufügen, um auf Entitäten an den Enden der Zuordnung zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="41e3e-134">If you add no navigation properties, you must choose to add foreign key properties in order to access entities at the ends of the association.</span></span>
    
3.  <span data-ttu-id="41e3e-135">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="41e3e-135">Click **OK**.</span></span>

### <a name="to-delete-an-association"></a><span data-ttu-id="41e3e-136">So löschen Sie eine Zuordnung</span><span class="sxs-lookup"><span data-stu-id="41e3e-136">To delete an association</span></span>

<span data-ttu-id="41e3e-137">Um eine Zuordnung zu löschen, führen Sie einen der folgenden Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="41e3e-137">To delete an association do one of the following:</span></span>

-   <span data-ttu-id="41e3e-138">Klicken Sie mit der rechten Maustaste auf die Verknüpfung auf der EF-Designer Oberfläche, und wählen Sie **Löschen**</span><span class="sxs-lookup"><span data-stu-id="41e3e-138">Right-click the association on the EF Designer surface and select **Delete**.</span></span>

- <span data-ttu-id="41e3e-139">- ODER -</span><span class="sxs-lookup"><span data-stu-id="41e3e-139">OR -</span></span>

-   <span data-ttu-id="41e3e-140">Wählen Sie eine oder mehrere Zuordnungen aus, und drücken Sie die ENTF-Taste.</span><span class="sxs-lookup"><span data-stu-id="41e3e-140">Select one or more associations and press the DELETE key.</span></span>

## <a name="include-foreign-key-properties-in-your-entities-referential-constraints"></a><span data-ttu-id="41e3e-141">Einschließen von Fremdschlüssel Eigenschaften in Ihre Entitäten (Referenzielle Einschränkungen)</span><span class="sxs-lookup"><span data-stu-id="41e3e-141">Include Foreign Key Properties in Your Entities (Referential Constraints)</span></span>

<span data-ttu-id="41e3e-142">Es wird empfohlen, Fremdschlüssel für Beziehungen in ihren Entitäten immer verfügbar zu machen.</span><span class="sxs-lookup"><span data-stu-id="41e3e-142">It is recommended that you always expose foreign keys for relationships in your entities.</span></span> <span data-ttu-id="41e3e-143">Entity Framework verwendet eine referenzielle Einschränkung, um zu identifizieren, dass eine Eigenschaft als Fremdschlüssel für eine Beziehung fungiert.</span><span class="sxs-lookup"><span data-stu-id="41e3e-143">Entity Framework uses a referential constraint to identify that a property acts as the foreign key for a relationship.</span></span>

<span data-ttu-id="41e3e-144">Wenn Sie beim Erstellen einer Beziehung das Kontrollkästchen ***Fremdschlüssel Eigenschaften der &lt; &gt; Entität Entitäts Typen Name hinzufügen*** aktiviert haben, wurde diese referenzielle Einschränkung für Sie hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="41e3e-144">If you checked the ***Add foreign key properties to the &lt;entity type name&gt; Entity*** checkbox when creating a relationship, this referential constraint was added for you.</span></span>

<span data-ttu-id="41e3e-145">Wenn Sie den EF-Designer verwenden, um eine referenzielle Einschränkung hinzuzufügen oder zu bearbeiten, fügt der EF-Designer ein **referentialeinschränkung**-   Element im CSDL-Inhalt der EDMX-Datei hinzu oder ändert es.</span><span class="sxs-lookup"><span data-stu-id="41e3e-145">When you use the EF Designer to add or edit a referential constraint, the EF Designer adds or modifies a **ReferentialConstraint** element in the CSDL content of the .edmx file.</span></span>

-   <span data-ttu-id="41e3e-146">Doppelklicken Sie auf die Zuordnung, die Sie bearbeiten möchten.</span><span class="sxs-lookup"><span data-stu-id="41e3e-146">Double-click the association that you want to edit.</span></span>
    <span data-ttu-id="41e3e-147">Das Dialogfeld **referenzielle Einschränkung**wird   angezeigt.</span><span class="sxs-lookup"><span data-stu-id="41e3e-147">The **Referential Constraint** dialog box appears.</span></span>
-   <span data-ttu-id="41e3e-148"> **Principal**   Wählen Sie in der Dropdown Liste Prinzipal die Prinzipal Entität in der referenziellen Einschränkung aus.</span><span class="sxs-lookup"><span data-stu-id="41e3e-148">From the **Principal** drop-down list, select the principal entity in the referential constraint.</span></span>
    <span data-ttu-id="41e3e-149">Die Schlüsseleigenschaften der Entität werden der Liste **Prinzipal Schlüssel**   im Dialogfeld hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="41e3e-149">The entity's key properties are added to the **Principal Key** list in the dialog box.</span></span>
-   <span data-ttu-id="41e3e-150"> **Dependent**   Wählen Sie in der Dropdown Liste abhängig die abhängige Entität in der referenziellen Einschränkung aus.</span><span class="sxs-lookup"><span data-stu-id="41e3e-150">From the **Dependent** drop-down list, select the dependent entity in the referential constraint.</span></span>
-   <span data-ttu-id="41e3e-151">Wählen Sie für jeden Prinzipal Schlüssel, der über einen abhängigen Schlüssel verfügt, einen entsprechenden abhängigen Schlüssel aus den Dropdown Listen in der Spalte **abhängige Schlüssel**aus   .</span><span class="sxs-lookup"><span data-stu-id="41e3e-151">For each principal key that has a dependent key, select a corresponding dependent key from the drop-down lists in the **Dependent Key** column.</span></span>

    ![Ref-Einschränkung](~/ef6/media/refconstraint.png)

-   <span data-ttu-id="41e3e-153">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="41e3e-153">Click **OK**.</span></span>

## <a name="create-and-edit-association-mappings"></a><span data-ttu-id="41e3e-154">Zuordnungs Zuordnungen erstellen und bearbeiten</span><span class="sxs-lookup"><span data-stu-id="41e3e-154">Create and Edit Association Mappings</span></span>

<span data-ttu-id="41e3e-155">Im Fenster **Mappingdetails**des EF-Designers können Sie angeben, wie eine Zuordnung der Datenbank zugeordnet werden soll   .</span><span class="sxs-lookup"><span data-stu-id="41e3e-155">You can specify how an association maps to the database in the **Mapping Details** window of the EF Designer.</span></span>

> [!NOTE]
> <span data-ttu-id="41e3e-156">Sie können nur Details für die Zuordnungen zuordnen, für die keine referenzielle Einschränkung festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="41e3e-156">You can only map details for the associations that do not have a referential constraint specified.</span></span> <span data-ttu-id="41e3e-157">Wenn eine referenzielle Einschränkung angegeben wird, ist eine Fremdschlüssel Eigenschaft in der Entität enthalten, und Sie können die Zuordnungs Details für die Entität verwenden, um die Spalte zu steuern, der der Fremdschlüssel zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="41e3e-157">If a referential constraint is specified then a foreign key property is included in the entity and you can use the Mapping Details for the entity to control which column the foreign key maps to.</span></span>

### <a name="create-an-association-mapping"></a><span data-ttu-id="41e3e-158">Erstellen einer Zuordnungs Zuordnung</span><span class="sxs-lookup"><span data-stu-id="41e3e-158">Create an association mapping</span></span>

-   <span data-ttu-id="41e3e-159">Klicken Sie in der Entwurfs Oberfläche mit der rechten Maustaste auf eine Zuordnung, und wählen Sie **Tabellen Zuordnung**.</span><span class="sxs-lookup"><span data-stu-id="41e3e-159">Right-click an association in the design surface and select **Table Mapping**.</span></span>
    <span data-ttu-id="41e3e-160">Dadurch wird die Zuordnungs Zuordnung im Fenster **Mappingdetails**angezeigt   .</span><span class="sxs-lookup"><span data-stu-id="41e3e-160">This displays the association mapping in the **Mapping Details** window.</span></span>
-   <span data-ttu-id="41e3e-161">Klicken Sie auf **Tabelle oder Sicht hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="41e3e-161">Click **Add a Table or View**.</span></span>
    <span data-ttu-id="41e3e-162">Eine Dropdownliste wird angezeigt, die die Tabellen im Speichermodell enthält.</span><span class="sxs-lookup"><span data-stu-id="41e3e-162">A drop-down list appears that includes all the tables in the storage model.</span></span>
-   <span data-ttu-id="41e3e-163">Wählen Sie die Tabelle aus, der die Zuordnung zugeordnet wird.</span><span class="sxs-lookup"><span data-stu-id="41e3e-163">Select the table to which the association will map.</span></span>
    <span data-ttu-id="41e3e-164">Im Fenster **Mappingdetails**werden   beide Enden der Zuordnung und die Schlüsseleigenschaften für den Entitätstyp an jedem **Ende**angezeigt.</span><span class="sxs-lookup"><span data-stu-id="41e3e-164">The **Mapping Details** window displays both ends of the association and the key properties for the entity type at each **End**.</span></span>
-   <span data-ttu-id="41e3e-165">Klicken Sie für jede Schlüsseleigenschaft auf das Feld **Spalte**   , und wählen Sie die Spalte aus, der die Eigenschaft zugeordnet werden soll.</span><span class="sxs-lookup"><span data-stu-id="41e3e-165">For each key property, click the **Column** field, and select the column to which the property will map.</span></span>

    ![Mappingdetails 4](~/ef6/media/mappingdetails4.png)

### <a name="edit-an-association-mapping"></a><span data-ttu-id="41e3e-167">Bearbeiten einer Zuordnungs Zuordnung</span><span class="sxs-lookup"><span data-stu-id="41e3e-167">Edit an association mapping</span></span>

-   <span data-ttu-id="41e3e-168">Klicken Sie in der Entwurfs Oberfläche mit der rechten Maustaste auf eine Zuordnung, und wählen Sie **Tabellen Zuordnung**.</span><span class="sxs-lookup"><span data-stu-id="41e3e-168">Right-click an association in the design surface and select **Table Mapping**.</span></span>
    <span data-ttu-id="41e3e-169">Dadurch wird die Zuordnungs Zuordnung im Fenster **Mappingdetails**angezeigt   .</span><span class="sxs-lookup"><span data-stu-id="41e3e-169">This displays the association mapping in the **Mapping Details** window.</span></span>
-   <span data-ttu-id="41e3e-170">Klicken Sie **auf dem &lt; Tabellen &gt; Namen auf Maps**.</span><span class="sxs-lookup"><span data-stu-id="41e3e-170">Click **Maps to &lt;Table Name&gt;**.</span></span>
    <span data-ttu-id="41e3e-171">Eine Dropdownliste wird angezeigt, die die Tabellen im Speichermodell enthält.</span><span class="sxs-lookup"><span data-stu-id="41e3e-171">A drop-down list appears that includes all the tables in the storage model.</span></span>
-   <span data-ttu-id="41e3e-172">Wählen Sie die Tabelle aus, der die Zuordnung zugeordnet wird.</span><span class="sxs-lookup"><span data-stu-id="41e3e-172">Select the table to which the association will map.</span></span>
    <span data-ttu-id="41e3e-173">Im Fenster **Mappingdetails**werden   beide Enden der Zuordnung und die Schlüsseleigenschaften für den Entitätstyp an jedem Ende angezeigt.</span><span class="sxs-lookup"><span data-stu-id="41e3e-173">The **Mapping Details** window displays both ends of the association and the key properties for the entity type at each End.</span></span>
-   <span data-ttu-id="41e3e-174">Klicken Sie für jede Schlüsseleigenschaft auf das Feld **Spalte**   , und wählen Sie die Spalte aus, der die Eigenschaft zugeordnet werden soll.</span><span class="sxs-lookup"><span data-stu-id="41e3e-174">For each key property, click the **Column** field, and select the column to which the property will map.</span></span>

## <a name="edit-and-delete-navigation-properties"></a><span data-ttu-id="41e3e-175">Bearbeiten und Löschen von Navigations Eigenschaften</span><span class="sxs-lookup"><span data-stu-id="41e3e-175">Edit and Delete Navigation Properties</span></span>

<span data-ttu-id="41e3e-176">Navigations Eigenschaften sind Verknüpfungs Eigenschaften, die verwendet werden, um die Entitäten an den Enden einer Zuordnung in einem Modell zu suchen.</span><span class="sxs-lookup"><span data-stu-id="41e3e-176">Navigation properties are shortcut properties that are used to locate the entities at the ends of an association in a model.</span></span> <span data-ttu-id="41e3e-177">Navigationseigenschaften können beim Erstellen einer Zuordnung zwischen zwei Entitätstypen erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="41e3e-177">Navigation properties can be created when you create an association between two entity types.</span></span>

#### <a name="to-edit-navigation-properties"></a><span data-ttu-id="41e3e-178">So bearbeiten Sie Navigations Eigenschaften</span><span class="sxs-lookup"><span data-stu-id="41e3e-178">To edit navigation properties</span></span>

-   <span data-ttu-id="41e3e-179">Wählen Sie auf der EF-Designer-Oberfläche eine Navigations Eigenschaft aus.</span><span class="sxs-lookup"><span data-stu-id="41e3e-179">Select a navigation property on the EF Designer surface.</span></span>
    <span data-ttu-id="41e3e-180">Informationen zur Navigations Eigenschaft werden im Visual Studio- **Eigenschaften**   Fenster angezeigt.</span><span class="sxs-lookup"><span data-stu-id="41e3e-180">Information about the navigation property is displayed in the Visual Studio **Properties** window.</span></span>
-   <span data-ttu-id="41e3e-181">Ändern Sie die Eigenschafts Einstellungen im **Eigenschaften**   Fenster.</span><span class="sxs-lookup"><span data-stu-id="41e3e-181">Change the property settings in the **Properties** window.</span></span>

#### <a name="to-delete-navigation-properties"></a><span data-ttu-id="41e3e-182">So löschen Sie Navigations Eigenschaften</span><span class="sxs-lookup"><span data-stu-id="41e3e-182">To delete navigation properties</span></span>

-   <span data-ttu-id="41e3e-183">Wenn Fremdschlüssel nicht für Entitätstypen im konzeptionellen Modell verfügbar gemacht werden, kann das Löschen einer Navigationseigenschaft dazu führen, dass die entsprechende Zuordnung nur in einer Richtung oder gar nicht traversiert werden kann.</span><span class="sxs-lookup"><span data-stu-id="41e3e-183">If foreign keys are not exposed on entity types in the conceptual model, deleting a navigation property may make the corresponding association traversable in only one direction or not traversable at all.</span></span>
-   <span data-ttu-id="41e3e-184">Klicken Sie in der EF Designer-Oberfläche mit der rechten Maustaste auf eine Navigations Eigenschaft, und wählen Sie **Löschen**</span><span class="sxs-lookup"><span data-stu-id="41e3e-184">Right-click a navigation property on the EF Designer surface and select **Delete**.</span></span>
