---
title: 'Komplexe Typen: EF-Designer-EF6'
description: 'Komplexe Typen: EF-Designer in Entity Framework 6'
author: divega
ms.date: 10/23/2016
ms.assetid: 9a8228ef-acfd-4575-860d-769d2c0e18a1
uid: ef6/modeling/designer/data-types/complex-types
ms.openlocfilehash: 87b62c119894486cbb54adfc8d3af6208a6618a8
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620601"
---
# <a name="complex-types---ef-designer"></a><span data-ttu-id="652ed-103">Komplexe Typen: EF-Designer</span><span class="sxs-lookup"><span data-stu-id="652ed-103">Complex Types - EF Designer</span></span>
<span data-ttu-id="652ed-104">In diesem Thema wird gezeigt, wie komplexe Typen mit dem Entity Framework Designer (EF-Designer) zugeordnet werden und wie Entitäten abgefragt werden, die Eigenschaften des komplexen Typs enthalten.</span><span class="sxs-lookup"><span data-stu-id="652ed-104">This topic shows how to map complex types with the Entity Framework Designer (EF Designer) and how to query for entities that contain properties of complex type.</span></span>

<span data-ttu-id="652ed-105">Die folgende Abbildung zeigt die Hauptfenster, die bei der Arbeit mit dem EF-Designer verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="652ed-105">The following image shows the main windows that are used when working with the EF Designer.</span></span>

![EF-Designer](~/ef6/media/efdesigner.png)

> [!NOTE]
> <span data-ttu-id="652ed-107">Beim Erstellen des konzeptionellen Modells können Warnungen zu nicht zugeordneten Entitäten und Zuordnungen in der Fehlerliste angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="652ed-107">When you build the conceptual model, warnings about unmapped entities and associations may appear in the Error List.</span></span> <span data-ttu-id="652ed-108">Sie können diese Warnungen ignorieren, da die Fehler nach dem Generieren der Datenbank aus dem Modell entfernt werden.</span><span class="sxs-lookup"><span data-stu-id="652ed-108">You can ignore these warnings because after you choose to generate the database from the model, the errors will go away.</span></span>

## <a name="what-is-a-complex-type"></a><span data-ttu-id="652ed-109">Was ist ein komplexer Typ?</span><span class="sxs-lookup"><span data-stu-id="652ed-109">What is a Complex Type</span></span>

<span data-ttu-id="652ed-110">Komplexe Typen sind nicht skalare Eigenschaften von Entitätstypen, mit deren Hilfe skalare Eigenschaften in Entitäten organisiert werden können.</span><span class="sxs-lookup"><span data-stu-id="652ed-110">Complex types are non-scalar properties of entity types that enable scalar properties to be organized within entities.</span></span> <span data-ttu-id="652ed-111">Wie Entitäten bestehen komplexe Typen aus skalaren Eigenschaften oder anderen Eigenschaften von komplexem Typ.</span><span class="sxs-lookup"><span data-stu-id="652ed-111">Like entities, complex types consist of scalar properties or other complex type properties.</span></span>

<span data-ttu-id="652ed-112">Beachten Sie Folgendes, wenn Sie mit Objekten arbeiten, die komplexe Typen darstellen:</span><span class="sxs-lookup"><span data-stu-id="652ed-112">When you work with objects that represent complex types, be aware of the following:</span></span>

-   <span data-ttu-id="652ed-113">Komplexe Typen verfügen über keine Schlüssel und können daher nicht unabhängig vorhanden sein.</span><span class="sxs-lookup"><span data-stu-id="652ed-113">Complex types do not have keys and therefore cannot exist independently.</span></span> <span data-ttu-id="652ed-114">Komplexe Typen können nur Eigenschaften von Entitätstypen oder anderen komplexen Typen sein.</span><span class="sxs-lookup"><span data-stu-id="652ed-114">Complex types can only exist as properties of entity types or other complex types.</span></span>
-   <span data-ttu-id="652ed-115">Komplexe Typen können nicht an Zuordnungen teilnehmen und keine Navigations Eigenschaften enthalten.</span><span class="sxs-lookup"><span data-stu-id="652ed-115">Complex types cannot participate in associations and cannot contain navigation properties.</span></span>
-   <span data-ttu-id="652ed-116">Eigenschaften komplexer Typen dürfen nicht **null**sein.</span><span class="sxs-lookup"><span data-stu-id="652ed-116">Complex type properties cannot be **null**.</span></span> <span data-ttu-id="652ed-117">Eine **InvalidOperationException **tritt auf, wenn **dbcontext. SaveChanges**   aufgerufen wird und ein komplexes NULL-Objekt gefunden wird.</span><span class="sxs-lookup"><span data-stu-id="652ed-117">An **InvalidOperationException **occurs when **DbContext.SaveChanges** is called and a null complex object is encountered.</span></span> <span data-ttu-id="652ed-118">Skalare Eigenschaften komplexer Objekte können **null**sein.</span><span class="sxs-lookup"><span data-stu-id="652ed-118">Scalar properties of complex objects can be **null**.</span></span>
-   <span data-ttu-id="652ed-119">Komplexe Typen können nicht von anderen komplexen Typen erben.</span><span class="sxs-lookup"><span data-stu-id="652ed-119">Complex types cannot inherit from other complex types.</span></span>
-   <span data-ttu-id="652ed-120">Sie müssen den komplexen Typ als **Klasse**definieren.</span><span class="sxs-lookup"><span data-stu-id="652ed-120">You must define the complex type as a **class**.</span></span> 
-   <span data-ttu-id="652ed-121">EF erkennt Änderungen an Membern für ein Objekt komplexer Typen, wenn **dbcontext. DetectChanges** aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="652ed-121">EF detects changes to members on a complex type object when **DbContext.DetectChanges** is called.</span></span> <span data-ttu-id="652ed-122">Entity Framework ruft **DetectChanges** automatisch auf, wenn die folgenden Member aufgerufen werden: **dbset. Find**, **dbset. local**, **dbset. Remove**, **dbset. Add**, **dbset. Attach**, **dbcontext. SaveChanges**, **dbcontext. getvalidationerrors**, **dbcontext. Entry**, **dbchangetracker. Entries**.</span><span class="sxs-lookup"><span data-stu-id="652ed-122">Entity Framework calls **DetectChanges** automatically when the following members are called: **DbSet.Find**, **DbSet.Local**, **DbSet.Remove**, **DbSet.Add**, **DbSet.Attach**, **DbContext.SaveChanges**, **DbContext.GetValidationErrors**, **DbContext.Entry**, **DbChangeTracker.Entries**.</span></span>

## <a name="refactor-an-entitys-properties-into-new-complex-type"></a><span data-ttu-id="652ed-123">Umgestalten der Eigenschaften einer Entität in einen neuen komplexen Typ</span><span class="sxs-lookup"><span data-stu-id="652ed-123">Refactor an Entity’s Properties into New Complex Type</span></span>

<span data-ttu-id="652ed-124">Wenn Sie bereits über eine Entität im konzeptionellen Modell verfügen, können Sie einige der Eigenschaften in eine komplexe Typeigenschaft umgestalten.</span><span class="sxs-lookup"><span data-stu-id="652ed-124">If you already have an entity in your conceptual model you may want to refactor some of the properties into a complex type property.</span></span>

<span data-ttu-id="652ed-125">Wählen Sie auf der Designer Oberfläche eine oder mehrere Eigenschaften (mit Ausnahme der Navigations Eigenschaften) einer Entität aus, klicken Sie dann mit der rechten Maustaste, und wählen Sie **umgestalten- &gt; in neuen komplexen Typ verschieben**aus.</span><span class="sxs-lookup"><span data-stu-id="652ed-125">On the designer surface, select one or more properties (excluding navigation properties) of an entity, then right-click and select **Refactor -&gt; Move to New Complex Type**.</span></span>

![Umgestalten in neuen komplexen Typ](~/ef6/media/refactor.png)

<span data-ttu-id="652ed-127">Ein neuer komplexer Typ mit den ausgewählten Eigenschaften wird dem **Modell Browser**hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="652ed-127">A new complex type with the selected properties is added to the **Model Browser**.</span></span> <span data-ttu-id="652ed-128">Dem komplexen Typ wird ein Standardname zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="652ed-128">The complex type is given a default name.</span></span>

<span data-ttu-id="652ed-129">Die ausgewählten Eigenschaften werden durch eine komplexe Eigenschaft des neu erstellten Typs ersetzt.</span><span class="sxs-lookup"><span data-stu-id="652ed-129">A complex property of the newly created type replaces the selected properties.</span></span> <span data-ttu-id="652ed-130">Alle Eigenschaftenzuordnungen werden beibehalten.</span><span class="sxs-lookup"><span data-stu-id="652ed-130">All property mappings are preserved.</span></span>

![Umgestalten in den neuen komplexen Typ 2](~/ef6/media/refactor2.png)

## <a name="create-a-new-complex-type"></a><span data-ttu-id="652ed-132">Neuen komplexen Typ erstellen</span><span class="sxs-lookup"><span data-stu-id="652ed-132">Create a New Complex Type</span></span>

<span data-ttu-id="652ed-133">Sie können auch einen neuen komplexen Typ erstellen, der keine Eigenschaften einer vorhandenen Entität enthält.</span><span class="sxs-lookup"><span data-stu-id="652ed-133">You can also create a new complex type that does not contain properties of an existing entity.</span></span>

<span data-ttu-id="652ed-134">Klicken Sie im Modell Browser mit der rechten Maustaste auf den Ordner **Complex Types** , und zeigen Sie auf **AddNew Complex Type...**.</span><span class="sxs-lookup"><span data-stu-id="652ed-134">Right-click the **Complex Types** folder in the Model Browser, point to **AddNew Complex Type…**.</span></span> <span data-ttu-id="652ed-135">Alternativ können Sie den Ordner **komplexe Typen** auswählen und die **Einfügetaste**   auf der Tastatur drücken.</span><span class="sxs-lookup"><span data-stu-id="652ed-135">Alternatively, you can select the **Complex Types** folder and press the **Insert** key on your keyboard.</span></span>

![Neuen komplexen Typ hinzufügen](~/ef6/media/addnewcomplextype.png)

<span data-ttu-id="652ed-137">Dem Ordner wird ein neuer komplexer Typ mit einem Standardnamen hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="652ed-137">A new complex type is added to the folder with a default name.</span></span> <span data-ttu-id="652ed-138">Sie können dem Typ jetzt Eigenschaften hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="652ed-138">You can now add properties to the type.</span></span>

## <a name="add-properties-to-a-complex-type"></a><span data-ttu-id="652ed-139">Hinzufügen von Eigenschaften zu einem komplexen Typ</span><span class="sxs-lookup"><span data-stu-id="652ed-139">Add Properties to a Complex Type</span></span>

<span data-ttu-id="652ed-140">Bei den Eigenschaften eines komplexen Typs kann es sich um skalare Typen oder vorhandene komplexe Typen handeln.</span><span class="sxs-lookup"><span data-stu-id="652ed-140">Properties of a complex type can be scalar types or existing complex types.</span></span> <span data-ttu-id="652ed-141">Für die Eigenschaften eines komplexen Typs können jedoch keine Zirkelverweise verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="652ed-141">However, complex type properties cannot have circular references.</span></span> <span data-ttu-id="652ed-142">Beispielsweise kann ein komplexer Typ " **onsitecourtendetails**"   keine Eigenschaft des komplexen Typs " **onsitecourtendetails**" aufweisen.</span><span class="sxs-lookup"><span data-stu-id="652ed-142">For example, a complex type **OnsiteCourseDetails** cannot have a property of complex type **OnsiteCourseDetails**.</span></span>

<span data-ttu-id="652ed-143">Einem komplexen Typ können anhand der unten aufgeführten Methoden Eigenschaften hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="652ed-143">You can add a property to a complex type in any of the ways listed below.</span></span>

-   <span data-ttu-id="652ed-144">Klicken Sie im Modell Browser mit der rechten Maustaste auf einen komplexen Typ, zeigen Sie auf **Hinzufügen**, zeigen Sie auf **skalare Eigenschaft**   oder **komplexe Eigenschaft**, und wählen Sie dann den gewünschten Eigenschaftentyp aus.</span><span class="sxs-lookup"><span data-stu-id="652ed-144">Right-click a complex type in the Model Browser, point to **Add**, then point to **Scalar Property** or **Complex Property**, then select the desired property type.</span></span> <span data-ttu-id="652ed-145">Alternativ können Sie einen komplexen Typ auswählen und dann die **Einfügetaste**   auf der Tastatur drücken.</span><span class="sxs-lookup"><span data-stu-id="652ed-145">Alternatively, you can select a complex type and then press the **Insert** key on your keyboard.</span></span>  

    ![Hinzufügen von Eigenschaften zu einem komplexen Typ](~/ef6/media/addpropertiestocomplextype.png)

    <span data-ttu-id="652ed-147">Dem komplexen Typ wird eine neue Eigenschaft mit einem Standardnamen hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="652ed-147">A new property is added to the complex type with a default name.</span></span>

- <span data-ttu-id="652ed-148">- ODER -</span><span class="sxs-lookup"><span data-stu-id="652ed-148">OR -</span></span>

-   <span data-ttu-id="652ed-149">Klicken Sie mit der rechten Maustaste auf eine Entitäts Eigenschaft auf der **EF-Designer** -Oberfläche, und wählen Sie **Kopieren** **aus. Klicken Sie dann**mit der rechten Maustaste auf den komplexen Typ im **Modell Browser**</span><span class="sxs-lookup"><span data-stu-id="652ed-149">Right-click an entity property on the **EF  Designer** surface and select **Copy**, then right-click the complex type in the **Model Browser** and select **Paste**.</span></span>

## <a name="rename-a-complex-type"></a><span data-ttu-id="652ed-150">Umbenennen eines komplexen Typs</span><span class="sxs-lookup"><span data-stu-id="652ed-150">Rename a Complex Type</span></span>

<span data-ttu-id="652ed-151">Wenn Sie einen komplexen Typ umbenennen, werden alle Verweise auf den Typ im gesamten Projekt aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="652ed-151">When you rename a complex type, all references to the type are updated throughout the project.</span></span>

-   <span data-ttu-id="652ed-152">Doppelklicken Sie im **Modell Browser**langsam auf einen komplexen Typ.</span><span class="sxs-lookup"><span data-stu-id="652ed-152">Slowly double-click a complex type in the **Model Browser**.</span></span>
    <span data-ttu-id="652ed-153">Der Name wird markiert und kann bearbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="652ed-153">The name will be selected and in edit mode.</span></span>

- <span data-ttu-id="652ed-154">- ODER -</span><span class="sxs-lookup"><span data-stu-id="652ed-154">OR -</span></span>

-   <span data-ttu-id="652ed-155">Klicken Sie im **Modell Browser** mit der rechten Maustaste auf einen komplexen Typ, und wählen Sie **Umbenennen**.</span><span class="sxs-lookup"><span data-stu-id="652ed-155">Right-click a complex type in the **Model Browser** and select **Rename**.</span></span>

- <span data-ttu-id="652ed-156">- ODER -</span><span class="sxs-lookup"><span data-stu-id="652ed-156">OR -</span></span>

-   <span data-ttu-id="652ed-157">Markieren Sie im Modellbrowser einen komplexen Typ, und drücken Sie die F2-TASTE.</span><span class="sxs-lookup"><span data-stu-id="652ed-157">Select a complex type in the Model Browser and press the F2 key.</span></span>

- <span data-ttu-id="652ed-158">- ODER -</span><span class="sxs-lookup"><span data-stu-id="652ed-158">OR -</span></span>

-   <span data-ttu-id="652ed-159">Klicken Sie im **Modell Browser** mit der rechten Maustaste auf einen komplexen Typ, und wählen Sie **Eigenschaften**aus.</span><span class="sxs-lookup"><span data-stu-id="652ed-159">Right-click a complex type in the **Model Browser** and select **Properties**.</span></span> <span data-ttu-id="652ed-160">Bearbeiten Sie den Namen im **Eigenschaften**   Fenster.</span><span class="sxs-lookup"><span data-stu-id="652ed-160">Edit the name in the **Properties** window.</span></span>

## <a name="add-an-existing-complex-type-to-an-entity-and-map-its-properties-to-table-columns"></a><span data-ttu-id="652ed-161">Fügen Sie einer Entität einen vorhandenen komplexen Typ hinzu, und ordnen Sie die zugehörigen Eigenschaften Tabellen Spalten zu.</span><span class="sxs-lookup"><span data-stu-id="652ed-161">Add an Existing Complex Type to an Entity and Map its Properties to Table Columns</span></span>

1.  <span data-ttu-id="652ed-162">Klicken Sie mit der rechten Maustaste auf eine Entität, zeigen Sie auf **Neu hinzufügen**, und wählen Sie **komplexe Eigenschaft**</span><span class="sxs-lookup"><span data-stu-id="652ed-162">Right-click an entity, point to **Add New**, and select **Complex Property**.</span></span>
    <span data-ttu-id="652ed-163">Der Entität wird eine Eigenschaft eines komplexen Typs mit einem Standardnamen hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="652ed-163">A complex type property with a default name is added to the entity.</span></span> <span data-ttu-id="652ed-164">Der Eigenschaft wird ein Standardtyp zugewiesen (ausgewählt aus den vorhandenen komplexen Typen).</span><span class="sxs-lookup"><span data-stu-id="652ed-164">A default type (chosen from the existing complex types) is assigned to the property.</span></span>
2.  <span data-ttu-id="652ed-165">Weisen Sie der-Eigenschaft im **Eigenschaften**Fenster den gewünschten Typ zu   .</span><span class="sxs-lookup"><span data-stu-id="652ed-165">Assign the desired type to the property in the **Properties** window.</span></span>
    <span data-ttu-id="652ed-166">Nachdem Sie einer Entität eine Eigenschaft eines komplexen Typs hinzugefügt haben, müssen Sie die zugehörigen Eigenschaften Tabellenspalten zuordnen.</span><span class="sxs-lookup"><span data-stu-id="652ed-166">After adding a complex type property to an entity, you must map its properties to table columns.</span></span>
3.  <span data-ttu-id="652ed-167">Klicken Sie in der Entwurfs Oberfläche oder im **Modell Browser**mit der rechten Maustaste auf einen Entitätstyp,   und wählen Sie **Tabellen**Zuordnungen aus.</span><span class="sxs-lookup"><span data-stu-id="652ed-167">Right-click an entity type on the design surface or in the **Model Browser** and select **Table Mappings**.</span></span>
    <span data-ttu-id="652ed-168">Die Tabellen Zuordnungen werden im Fenster **Mappingdetails**angezeigt   .</span><span class="sxs-lookup"><span data-stu-id="652ed-168">The table mappings are displayed in the **Mapping Details** window.</span></span>
4.  <span data-ttu-id="652ed-169">Erweitern Sie den Knoten \*\*maps to &lt; Table Name &gt; \*\*   .</span><span class="sxs-lookup"><span data-stu-id="652ed-169">Expand the **Maps to &lt;Table Name&gt;** node.</span></span>
    <span data-ttu-id="652ed-170">Ein Knoten **Spalten**Zuordnungen wird   angezeigt.</span><span class="sxs-lookup"><span data-stu-id="652ed-170">A **Column Mappings** node appears.</span></span>
5.  <span data-ttu-id="652ed-171">Erweitern Sie den Knoten **Spalten**Zuordnungen   .</span><span class="sxs-lookup"><span data-stu-id="652ed-171">Expand the **Column Mappings** node.</span></span>
    <span data-ttu-id="652ed-172">Eine Liste aller Spalten in der Tabelle wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="652ed-172">A list of all the columns in the table appears.</span></span> <span data-ttu-id="652ed-173">Die Standardeigenschaften (sofern vorhanden), denen die Spalten zugeordnet sind, werden unter der Überschrift **Wert/Eigenschaft**aufgelistet   .</span><span class="sxs-lookup"><span data-stu-id="652ed-173">The default properties (if any) to which the columns map are listed under the **Value/Property** heading.</span></span>
6.  <span data-ttu-id="652ed-174">Wählen Sie die Spalte aus, die Sie zuordnen möchten, und klicken Sie dann mit der rechten Maustaste auf das entsprechende Feld **Wert/Eigenschaft**   .</span><span class="sxs-lookup"><span data-stu-id="652ed-174">Select the column you want to map, and then right-click the corresponding **Value/Property** field.</span></span>
    <span data-ttu-id="652ed-175">Eine Dropdownliste aller skalaren Eigenschaften wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="652ed-175">A drop-down list of all the scalar properties is displayed.</span></span>
7.  <span data-ttu-id="652ed-176">Wählen Sie die entsprechende Eigenschaft aus.</span><span class="sxs-lookup"><span data-stu-id="652ed-176">Select the appropriate property.</span></span>

    ![Komplexer Kartentyp](~/ef6/media/mapcomplextype.png)

8.  <span data-ttu-id="652ed-178">Wiederholen Sie die Schritte 6 und 7 für jede Tabellenspalte.</span><span class="sxs-lookup"><span data-stu-id="652ed-178">Repeat steps 6 and 7 for each table column.</span></span>

>[!NOTE]
> <span data-ttu-id="652ed-179">Um eine Spalten Zuordnung zu löschen, wählen Sie die Spalte aus, die Sie zuordnen möchten, und klicken Sie dann auf das Feld **Wert/Eigenschaft**   .</span><span class="sxs-lookup"><span data-stu-id="652ed-179">To delete a column mapping, select the column that you want to map, and then click the **Value/Property** field.</span></span> <span data-ttu-id="652ed-180">Wählen Sie dann in der Dropdown Liste die Option **Löschen** aus.</span><span class="sxs-lookup"><span data-stu-id="652ed-180">Then, select **Delete** from the drop-down list.</span></span>

## <a name="map-a-function-import-to-a-complex-type"></a><span data-ttu-id="652ed-181">Zuordnen eines Funktions Imports zu einem komplexen Typ</span><span class="sxs-lookup"><span data-stu-id="652ed-181">Map a Function Import to a Complex Type</span></span>

<span data-ttu-id="652ed-182">Funktionsimporte basieren auf gespeicherten Prozeduren.</span><span class="sxs-lookup"><span data-stu-id="652ed-182">Function imports are based on stored procedures.</span></span> <span data-ttu-id="652ed-183">Um einen Funktionsimport einem komplexen Typ zuzuordnen, muss die Zahl der von der entsprechenden gespeicherten Prozedur zurückgegebenen Spalten der Zahl der Eigenschaften des komplexen Typs entsprechen, und die Spalten müssen einen Speichertyp aufweisen, der mit den Typen der Eigenschaften kompatibel ist.</span><span class="sxs-lookup"><span data-stu-id="652ed-183">To map a function import to a complex type, the columns returned by the corresponding stored procedure must match the properties of the complex type in number and must have storage types that are compatible with the property types.</span></span>

-   <span data-ttu-id="652ed-184">Doppelklicken Sie auf eine importierte Funktion, die einem komplexen Typ zugeordnet werden soll.</span><span class="sxs-lookup"><span data-stu-id="652ed-184">Double-click on an imported function that you want to map to a complex type.</span></span>

    ![Funktions Importe](~/ef6/media/functionimports.png)

-   <span data-ttu-id="652ed-186">Geben Sie wie folgt die Einstellungen für den neuen Funktionsimport ein:</span><span class="sxs-lookup"><span data-stu-id="652ed-186">Fill in the settings for the new function import, as follows:</span></span>
    -   <span data-ttu-id="652ed-187">Geben Sie im Feld **Name der gespeicherten Prozedur**die gespeicherte Prozedur an, für die Sie einen Funktions Import erstellen   .</span><span class="sxs-lookup"><span data-stu-id="652ed-187">Specify the stored procedure for which you are creating a function import in the **Stored Procedure Name** field.</span></span> <span data-ttu-id="652ed-188">Dieses Feld wird als Dropdownliste angezeigt, die alle gespeicherten Prozeduren im Speichermodell enthält.</span><span class="sxs-lookup"><span data-stu-id="652ed-188">This field is a drop-down list that displays all the stored procedures in the storage model.</span></span>
    -   <span data-ttu-id="652ed-189">Geben Sie den Namen des Funktions Imports im Feld **Funktions Import Name**an   .</span><span class="sxs-lookup"><span data-stu-id="652ed-189">Specify the name of the function import in the **Function Import Name** field.</span></span>
    -   <span data-ttu-id="652ed-190">Wählen Sie **Komplex**   als Rückgabetyp aus, und geben Sie dann den spezifischen komplexen Rückgabetyp an, indem Sie den entsprechenden Typ aus der Dropdown Liste auswählen.</span><span class="sxs-lookup"><span data-stu-id="652ed-190">Select **Complex** as the return type and then specify the specific complex return type by choosing the appropriate type from the drop-down list.</span></span>

        ![Funktions Import bearbeiten](~/ef6/media/editfunctionimport.png)

-   <span data-ttu-id="652ed-192">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="652ed-192">Click **OK**.</span></span>
    <span data-ttu-id="652ed-193">Der Funktionsimport-Eintrag wird im konzeptionellen Modell erstellt.</span><span class="sxs-lookup"><span data-stu-id="652ed-193">The function import entry is created in the conceptual model.</span></span>

### <a name="customize-column-mapping-for-function-import"></a><span data-ttu-id="652ed-194">Anpassen der Spalten Zuordnung für den Funktions Import</span><span class="sxs-lookup"><span data-stu-id="652ed-194">Customize Column Mapping for Function Import</span></span>

-   <span data-ttu-id="652ed-195">Klicken Sie im Modell Browser mit der rechten Maustaste auf den Funktions Import, und wählen Sie **Funktions Import Zuordnung**aus.</span><span class="sxs-lookup"><span data-stu-id="652ed-195">Right-click the function import in the Model Browser and select **Function Import Mapping**.</span></span>
    <span data-ttu-id="652ed-196">Das Fenster **Mappingdetails**wird   angezeigt und zeigt die Standard Zuordnung für den Funktions Import an.</span><span class="sxs-lookup"><span data-stu-id="652ed-196">The **Mapping Details** window appears and shows the default mapping for the function import.</span></span> <span data-ttu-id="652ed-197">Pfeilsymbole geben die Zuordnungen der Spaltenwerte zu den Eigenschaftswerten an.</span><span class="sxs-lookup"><span data-stu-id="652ed-197">Arrows indicate the mappings between column values and property values.</span></span> <span data-ttu-id="652ed-198">Standardmäßig wird angenommen, dass die Spaltennamen mit den Namen der Eigenschaften des komplexen Typs identisch sind.</span><span class="sxs-lookup"><span data-stu-id="652ed-198">By default, the column names are assumed to be the same as the complex type's property names.</span></span> <span data-ttu-id="652ed-199">Die Standardspaltennamen werden in grauem Text angezeigt.</span><span class="sxs-lookup"><span data-stu-id="652ed-199">The default column names appear in gray text.</span></span>
-   <span data-ttu-id="652ed-200">Ändern Sie, falls erforderlich, die Spaltennamen so, dass sie mit den Spaltennamen übereinstimmen, die von der gespeicherten Prozedur zurückgegeben werden, die dem Funktionsimport entspricht.</span><span class="sxs-lookup"><span data-stu-id="652ed-200">If necessary, change the column names to match the column names that are returned by the stored procedure that corresponds to the function import.</span></span>

## <a name="delete-a-complex-type"></a><span data-ttu-id="652ed-201">Löschen eines komplexen Typs</span><span class="sxs-lookup"><span data-stu-id="652ed-201">Delete a Complex Type</span></span>

<span data-ttu-id="652ed-202">Wenn Sie einen komplexen Typ löschen, wird der Typ aus dem konzeptionellen Modell gelöscht, und die Mappings für alle Instanzen des Typs werden gelöscht.</span><span class="sxs-lookup"><span data-stu-id="652ed-202">When you delete a complex type, the type is deleted from the conceptual model and mappings for all instances of the type are deleted.</span></span> <span data-ttu-id="652ed-203">Verweise auf den Typ werden jedoch nicht aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="652ed-203">However, references to the type are not updated.</span></span> <span data-ttu-id="652ed-204">Wenn eine Entität z. b. über eine komplexe Typeigenschaft vom Typ ComplexType1 und ComplexType1 im **Modell Browser**gelöscht wird, wird die zugehörige Entitäts Eigenschaft nicht aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="652ed-204">For example, if an entity has a complex type property of type ComplexType1 and ComplexType1 is deleted in the **Model Browser**, the corresponding entity property is not updated.</span></span> <span data-ttu-id="652ed-205">Das Modell wird nicht überprüft, weil es eine Entität enthält, die auf einen gelöschten komplexen Typ verweist.</span><span class="sxs-lookup"><span data-stu-id="652ed-205">The model will not validate  because it contains an entity that references a deleted complex type.</span></span> <span data-ttu-id="652ed-206">Verweise auf gelöschte komplexe Typen können mit dem Entity Designer aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="652ed-206">You can update or delete references to deleted complex types by using the Entity Designer.</span></span>

-   <span data-ttu-id="652ed-207">Klicken Sie im Modell Browser mit der rechten Maustaste auf einen komplexen Typ, und wählen Sie **Löschen**aus.</span><span class="sxs-lookup"><span data-stu-id="652ed-207">Right-click a complex type in the Model Browser and select **Delete**.</span></span>

- <span data-ttu-id="652ed-208">- ODER -</span><span class="sxs-lookup"><span data-stu-id="652ed-208">OR -</span></span>

-   <span data-ttu-id="652ed-209">Markieren Sie im Modellbrowser einen komplexen Typ, und drücken Sie dann die ENTF-TASTE auf der Tastatur.</span><span class="sxs-lookup"><span data-stu-id="652ed-209">Select a complex type in the Model Browser and press the Delete key on your keyboard.</span></span>

## <a name="query-for-entities-containing-properties-of-complex-type"></a><span data-ttu-id="652ed-210">Abfragen von Entitäten mit Eigenschaften des komplexen Typs</span><span class="sxs-lookup"><span data-stu-id="652ed-210">Query for Entities Containing Properties of Complex Type</span></span>

<span data-ttu-id="652ed-211">Der folgende Code zeigt, wie eine Abfrage ausgeführt wird, die eine Auflistung von Entitätstyp Objekten zurückgibt, die eine Eigenschaft eines komplexen Typs enthalten.</span><span class="sxs-lookup"><span data-stu-id="652ed-211">The following code shows how to execute a query that returns a collection of entity type objects that contain a complex type property.</span></span>

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
