---
title: Behandeln von Parallelitäts Konflikten EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 2318e4d3-f561-4720-bbc3-921556806476
ms.openlocfilehash: a99f824fe256a10b84f539a5339a09624315efa4
ms.sourcegitcommit: 59e3d5ce7dfb284457cf1c991091683b2d1afe9d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83672713"
---
# <a name="handling-concurrency-conflicts"></a><span data-ttu-id="46a9c-102">Behandlung von Parallelitätskonflikten</span><span class="sxs-lookup"><span data-stu-id="46a9c-102">Handling Concurrency Conflicts</span></span>
<span data-ttu-id="46a9c-103">Die optimistische Parallelität erfordert den optimistischen Versuch, ihre Entität in der Datenbank zu speichern, in der Hoffnung, dass sich die Daten seit dem Laden der Entität nicht geändert haben.</span><span class="sxs-lookup"><span data-stu-id="46a9c-103">Optimistic concurrency involves optimistically attempting to save your entity to the database in the hope that the data there has not changed since the entity was loaded.</span></span> <span data-ttu-id="46a9c-104">Wenn sich herausstellt, dass sich die Daten geändert haben, wird eine Ausnahme ausgelöst, und Sie müssen den Konflikt beheben, bevor Sie versuchen, die Daten erneut zu speichern.</span><span class="sxs-lookup"><span data-stu-id="46a9c-104">If it turns out that the data has changed then an exception is thrown and you must resolve the conflict before attempting to save again.</span></span> <span data-ttu-id="46a9c-105">In diesem Thema wird erläutert, wie solche Ausnahmen in Entity Framework behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="46a9c-105">This topic covers how to handle such exceptions in Entity Framework.</span></span> <span data-ttu-id="46a9c-106">Die in diesem Thema dargestellten Techniken gelten jeweils für Modelle, die mit Code First und dem EF-Designer erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="46a9c-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="46a9c-107">Dieser Beitrag ist nicht der richtige Ort, um eine vollständige Erläuterung der vollständigen Parallelität zu erörtern.</span><span class="sxs-lookup"><span data-stu-id="46a9c-107">This post is not the appropriate place for a full discussion of optimistic concurrency.</span></span> <span data-ttu-id="46a9c-108">In den folgenden Abschnitten wird davon ausgegangen, dass Sie die Parallelitäts Auflösung kennen und Muster für häufige Aufgaben anzeigen.</span><span class="sxs-lookup"><span data-stu-id="46a9c-108">The sections below assume some knowledge of concurrency resolution and show patterns for common tasks.</span></span>  

<span data-ttu-id="46a9c-109">Viele dieser Muster nutzen die Themen, die unter [Arbeiten mit Eigenschafts Werten](~/ef6/saving/change-tracking/property-values.md)erläutert werden.</span><span class="sxs-lookup"><span data-stu-id="46a9c-109">Many of these patterns make use of the topics discussed in [Working with Property Values](~/ef6/saving/change-tracking/property-values.md).</span></span>  

<span data-ttu-id="46a9c-110">Das Beheben von Parallelitäts Problemen bei der Verwendung unabhängiger Zuordnungen (wobei der Fremdschlüssel nicht einer Eigenschaft in der Entität zugeordnet ist) ist weitaus schwieriger als bei der Verwendung von Fremdschlüssel Zuordnungen.</span><span class="sxs-lookup"><span data-stu-id="46a9c-110">Resolving concurrency issues when you are using independent associations (where the foreign key is not mapped to a property in your entity) is much more difficult than when you are using foreign key associations.</span></span> <span data-ttu-id="46a9c-111">Wenn Sie in Ihrer Anwendung Parallelitäts Auflösung durchführen möchten, wird empfohlen, dass Sie den Entitäten immer Fremdschlüssel zuordnen.</span><span class="sxs-lookup"><span data-stu-id="46a9c-111">Therefore if you are going to do concurrency resolution in your application it is advised that you always map foreign keys into your entities.</span></span> <span data-ttu-id="46a9c-112">Bei allen folgenden Beispielen wird davon ausgegangen, dass Sie Fremdschlüssel Zuordnungen verwenden.</span><span class="sxs-lookup"><span data-stu-id="46a9c-112">All the examples below assume that you are using foreign key associations.</span></span>  

<span data-ttu-id="46a9c-113">Eine dbupdateconcurrency cyexception wird von SaveChanges ausgelöst, wenn beim Versuch, eine Entität zu speichern, die Fremdschlüssel Zuordnungen verwendet, eine Ausnahme der vollständigen Parallelität festgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="46a9c-113">A DbUpdateConcurrencyException is thrown by SaveChanges when an optimistic concurrency exception is detected while attempting to save an entity that uses foreign key associations.</span></span>  

## <a name="resolving-optimistic-concurrency-exceptions-with-reload-database-wins"></a><span data-ttu-id="46a9c-114">Auflösen von Ausnahmen der vollständigen Parallelität durch erneutes Laden (Datenbank gewinnt)</span><span class="sxs-lookup"><span data-stu-id="46a9c-114">Resolving optimistic concurrency exceptions with Reload (database wins)</span></span>  

<span data-ttu-id="46a9c-115">Die Methode zum erneuten Laden kann verwendet werden, um die aktuellen Werte der Entität mit den aktuellen Werten in der Datenbank zu überschreiben.</span><span class="sxs-lookup"><span data-stu-id="46a9c-115">The Reload method can be used to overwrite the current values of the entity with the values now in the database.</span></span> <span data-ttu-id="46a9c-116">Die Entität wird dann normalerweise in irgendeiner Form an den Benutzer zurückgegeben, und Sie müssen versuchen, Ihre Änderungen erneut vorzunehmen und erneut zu speichern.</span><span class="sxs-lookup"><span data-stu-id="46a9c-116">The entity is then typically given back to the user in some form and they must try to make their changes again and re-save.</span></span> <span data-ttu-id="46a9c-117">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="46a9c-117">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    blog.Name = "The New ADO.NET Blog";

    bool saveFailed;
    do
    {
        saveFailed = false;

        try
        {
            context.SaveChanges();
        }
        catch (DbUpdateConcurrencyException ex)
        {
            saveFailed = true;

            // Update the values of the entity that failed to save from the store
            ex.Entries.Single().Reload();
        }

    } while (saveFailed);
}
```  

<span data-ttu-id="46a9c-118">Eine gute Möglichkeit, eine Parallelitäts Ausnahme zu simulieren, besteht darin, einen Haltepunkt für den SaveChanges-Befehl festzulegen und anschließend eine Entität zu ändern, die in der Datenbank mit einem anderen Tool wie SQL Server Management Studio gespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="46a9c-118">A good way to simulate a concurrency exception is to set a breakpoint on the SaveChanges call and then modify an entity that is being saved in the database using another tool such as SQL Server Management Studio.</span></span> <span data-ttu-id="46a9c-119">Sie können auch eine Zeile vor "SaveChanges" einfügen, um die Datenbank direkt mithilfe von "SqlCommand" zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="46a9c-119">You could also insert a line before SaveChanges to update the database directly using SqlCommand.</span></span> <span data-ttu-id="46a9c-120">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="46a9c-120">For example:</span></span>  

``` csharp
context.Database.SqlCommand(
    "UPDATE dbo.Blogs SET Name = 'Another Name' WHERE BlogId = 1");
```  

<span data-ttu-id="46a9c-121">Die Entries-Methode für dbupdateconaccesscyexception gibt die dbentityentry-Instanzen für die Entitäten zurück, die nicht aktualisiert werden konnten.</span><span class="sxs-lookup"><span data-stu-id="46a9c-121">The Entries method on DbUpdateConcurrencyException returns the DbEntityEntry instances for the entities that failed to update.</span></span> <span data-ttu-id="46a9c-122">(Diese Eigenschaft gibt derzeit immer einen einzelnen Wert für Parallelitäts Probleme zurück.</span><span class="sxs-lookup"><span data-stu-id="46a9c-122">(This property currently always returns a single value for concurrency issues.</span></span> <span data-ttu-id="46a9c-123">Es können mehrere Werte für allgemeine Update Ausnahmen zurückgegeben werden.) Eine Alternative kann in einigen Situationen darin bestehen, Einträge für alle Entitäten abzurufen, die möglicherweise aus der Datenbank erneut geladen werden müssen, und für jede dieser Elemente den Befehl zum erneuten Laden aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="46a9c-123">It may return multiple values for general update exceptions.) An alternative for some situations might be to get entries for all entities that may need to be reloaded from the database and call reload for each of these.</span></span>  

## <a name="resolving-optimistic-concurrency-exceptions-as-client-wins"></a><span data-ttu-id="46a9c-124">Auflösen von Ausnahmen der vollständigen Parallelität als Client gewinnt</span><span class="sxs-lookup"><span data-stu-id="46a9c-124">Resolving optimistic concurrency exceptions as client wins</span></span>  

<span data-ttu-id="46a9c-125">Das obige Beispiel, das das erneute Laden verwendet, wird manchmal als Database WINS oder Store WINS bezeichnet, da die Werte in der Entität durch Werte aus der Datenbank überschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="46a9c-125">The example above that uses Reload is sometimes called database wins or store wins because the values in the entity are overwritten by values from the database.</span></span> <span data-ttu-id="46a9c-126">Manchmal möchten Sie möglicherweise das Gegenteil durchführen und die Werte in der Datenbank mit den Werten überschreiben, die sich derzeit in der Entität befinden.</span><span class="sxs-lookup"><span data-stu-id="46a9c-126">Sometimes you may wish to do the opposite and overwrite the values in the database with the values currently in the entity.</span></span> <span data-ttu-id="46a9c-127">Dies wird auch als Client WINS bezeichnet und kann durch das erhalten der aktuellen Daten Bank Werte und durch Festlegen der ursprünglichen Werte für die Entität erreicht werden.</span><span class="sxs-lookup"><span data-stu-id="46a9c-127">This is sometimes called client wins and can be done by getting the current database values and setting them as the original values for the entity.</span></span> <span data-ttu-id="46a9c-128">(Weitere Informationen zu aktuellen und ursprünglichen Werten finden Sie [unter Arbeiten mit Eigenschafts Werten](~/ef6/saving/change-tracking/property-values.md) .) Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="46a9c-128">(See [Working with Property Values](~/ef6/saving/change-tracking/property-values.md) for information on current and original values.) For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    blog.Name = "The New ADO.NET Blog";

    bool saveFailed;
    do
    {
        saveFailed = false;
        try
        {
            context.SaveChanges();
        }
        catch (DbUpdateConcurrencyException ex)
        {
            saveFailed = true;

            // Update original values from the database
            var entry = ex.Entries.Single();
            entry.OriginalValues.SetValues(entry.GetDatabaseValues());
        }

    } while (saveFailed);
}
```  

## <a name="custom-resolution-of-optimistic-concurrency-exceptions"></a><span data-ttu-id="46a9c-129">Benutzerdefinierte Auflösung von Ausnahmen bei optimistischer Parallelität</span><span class="sxs-lookup"><span data-stu-id="46a9c-129">Custom resolution of optimistic concurrency exceptions</span></span>  

<span data-ttu-id="46a9c-130">Manchmal möchten Sie möglicherweise die Werte in der Datenbank mit den Werten kombinieren, die derzeit in der Entität gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="46a9c-130">Sometimes you may want to combine the values currently in the database with the values currently in the entity.</span></span> <span data-ttu-id="46a9c-131">Dies erfordert in der Regel eine benutzerdefinierte Logik oder eine Benutzerinteraktion.</span><span class="sxs-lookup"><span data-stu-id="46a9c-131">This usually requires some custom logic or user interaction.</span></span> <span data-ttu-id="46a9c-132">Beispielsweise können Sie dem Benutzer ein Formular mit den aktuellen Werten, den Werten in der Datenbank und einem Standardsatz von aufgelösten Werten präsentieren.</span><span class="sxs-lookup"><span data-stu-id="46a9c-132">For example, you might present a form to the user containing the current values, the values in the database, and a default set of resolved values.</span></span> <span data-ttu-id="46a9c-133">Der Benutzer bearbeitet dann die aufgelösten Werte nach Bedarf, und es wären diese aufgelösten Werte, die in der Datenbank gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="46a9c-133">The user would then edit the resolved values as necessary and it would be these resolved values that get saved to the database.</span></span> <span data-ttu-id="46a9c-134">Dies kann mit den dbpropertyvalues-Objekten erfolgen, die von CurrentValues und getdatabasevalues für den Eintrag der Entität zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="46a9c-134">This can be done using the DbPropertyValues objects returned from CurrentValues and GetDatabaseValues on the entity’s entry.</span></span> <span data-ttu-id="46a9c-135">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="46a9c-135">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    blog.Name = "The New ADO.NET Blog";

    bool saveFailed;
    do
    {
        saveFailed = false;
        try
        {
            context.SaveChanges();
        }
        catch (DbUpdateConcurrencyException ex)
        {
            saveFailed = true;

            // Get the current entity values and the values in the database
            var entry = ex.Entries.Single();
            var currentValues = entry.CurrentValues;
            var databaseValues = entry.GetDatabaseValues();

            // Choose an initial set of resolved values. In this case we
            // make the default be the values currently in the database.
            var resolvedValues = databaseValues.Clone();

            // Have the user choose what the resolved values should be
            HaveUserResolveConcurrency(currentValues, databaseValues, resolvedValues);

            // Update the original values with the database values and
            // the current values with whatever the user choose.
            entry.OriginalValues.SetValues(databaseValues);
            entry.CurrentValues.SetValues(resolvedValues);
        }
    } while (saveFailed);
}

public void HaveUserResolveConcurrency(DbPropertyValues currentValues,
                                       DbPropertyValues databaseValues,
                                       DbPropertyValues resolvedValues)
{
    // Show the current, database, and resolved values to the user and have
    // them edit the resolved values to get the correct resolution.
}
```  

## <a name="custom-resolution-of-optimistic-concurrency-exceptions-using-objects"></a><span data-ttu-id="46a9c-136">Benutzerdefinierte Auflösung von Ausnahmen mit optimistischer Parallelität mithilfe von Objekten</span><span class="sxs-lookup"><span data-stu-id="46a9c-136">Custom resolution of optimistic concurrency exceptions using objects</span></span>  

<span data-ttu-id="46a9c-137">Der obige Code verwendet dbpropertyvalues-Instanzen, um aktuelle, Datenbank-und aufgelöste Werte zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="46a9c-137">The code above uses DbPropertyValues instances for passing around current, database, and resolved values.</span></span> <span data-ttu-id="46a9c-138">Manchmal ist es möglicherweise einfacher, Instanzen des Entitäts Typs für dieses zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="46a9c-138">Sometimes it may be easier to use instances of your entity type for this.</span></span> <span data-ttu-id="46a9c-139">Dies kann mithilfe der Methoden "-Objekt" und "SetValues" von dbpropertyvalues erreicht werden.</span><span class="sxs-lookup"><span data-stu-id="46a9c-139">This can be done using the ToObject and SetValues methods of DbPropertyValues.</span></span> <span data-ttu-id="46a9c-140">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="46a9c-140">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    blog.Name = "The New ADO.NET Blog";

    bool saveFailed;
    do
    {
        saveFailed = false;
        try
        {
            context.SaveChanges();
        }
        catch (DbUpdateConcurrencyException ex)
        {
            saveFailed = true;

            // Get the current entity values and the values in the database
            // as instances of the entity type
            var entry = ex.Entries.Single();
            var databaseValues = entry.GetDatabaseValues();
            var databaseValuesAsBlog = (Blog)databaseValues.ToObject();

            // Choose an initial set of resolved values. In this case we
            // make the default be the values currently in the database.
            var resolvedValuesAsBlog = (Blog)databaseValues.ToObject();

            // Have the user choose what the resolved values should be
            HaveUserResolveConcurrency((Blog)entry.Entity,
                                       databaseValuesAsBlog,
                                       resolvedValuesAsBlog);

            // Update the original values with the database values and
            // the current values with whatever the user choose.
            entry.OriginalValues.SetValues(databaseValues);
            entry.CurrentValues.SetValues(resolvedValuesAsBlog);
        }

    } while (saveFailed);
}

public void HaveUserResolveConcurrency(Blog entity,
                                       Blog databaseValues,
                                       Blog resolvedValues)
{
    // Show the current, database, and resolved values to the user and have
    // them update the resolved values to get the correct resolution.
}
```  
