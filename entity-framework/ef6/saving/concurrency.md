---
title: Behandeln von Parallelitäts Konflikten EF6
description: Behandeln von Parallelitäts Konflikten in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/saving/concurrency
ms.openlocfilehash: 0cec285ab6071120e162567506d397a23c299177
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064509"
---
# <a name="handling-concurrency-conflicts-ef6"></a>Behandeln von Parallelitäts Konflikten (EF6)

Die optimistische Parallelität erfordert den optimistischen Versuch, ihre Entität in der Datenbank zu speichern, in der Hoffnung, dass sich die Daten seit dem Laden der Entität nicht geändert haben. Wenn sich herausstellt, dass sich die Daten geändert haben, wird eine Ausnahme ausgelöst, und Sie müssen den Konflikt beheben, bevor Sie versuchen, die Daten erneut zu speichern. In diesem Thema wird erläutert, wie solche Ausnahmen in Entity Framework behandelt werden. Die in diesem Thema dargestellten Techniken gelten jeweils für Modelle, die mit Code First und dem EF-Designer erstellt wurden.  

Dieser Beitrag ist nicht der richtige Ort, um eine vollständige Erläuterung der vollständigen Parallelität zu erörtern. In den folgenden Abschnitten wird davon ausgegangen, dass Sie die Parallelitäts Auflösung kennen und Muster für häufige Aufgaben anzeigen.  

Viele dieser Muster nutzen die Themen, die unter [Arbeiten mit Eigenschafts Werten](xref:ef6/saving/change-tracking/property-values)erläutert werden.  

Das Beheben von Parallelitäts Problemen bei der Verwendung unabhängiger Zuordnungen (wobei der Fremdschlüssel nicht einer Eigenschaft in der Entität zugeordnet ist) ist weitaus schwieriger als bei der Verwendung von Fremdschlüssel Zuordnungen. Wenn Sie in Ihrer Anwendung Parallelitäts Auflösung durchführen möchten, wird empfohlen, dass Sie den Entitäten immer Fremdschlüssel zuordnen. Bei allen folgenden Beispielen wird davon ausgegangen, dass Sie Fremdschlüssel Zuordnungen verwenden.  

Eine dbupdateconcurrency cyexception wird von SaveChanges ausgelöst, wenn beim Versuch, eine Entität zu speichern, die Fremdschlüssel Zuordnungen verwendet, eine Ausnahme der vollständigen Parallelität festgestellt wird.  

## <a name="resolving-optimistic-concurrency-exceptions-with-reload-database-wins"></a>Auflösen von Ausnahmen der vollständigen Parallelität durch erneutes Laden (Datenbank gewinnt)  

Die Methode zum erneuten Laden kann verwendet werden, um die aktuellen Werte der Entität mit den aktuellen Werten in der Datenbank zu überschreiben. Die Entität wird dann normalerweise in irgendeiner Form an den Benutzer zurückgegeben, und Sie müssen versuchen, Ihre Änderungen erneut vorzunehmen und erneut zu speichern. Beispiel:  

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

Eine gute Möglichkeit, eine Parallelitäts Ausnahme zu simulieren, besteht darin, einen Haltepunkt für den SaveChanges-Befehl festzulegen und anschließend eine Entität zu ändern, die in der Datenbank mit einem anderen Tool wie SQL Server Management Studio gespeichert wird. Sie können auch eine Zeile vor "SaveChanges" einfügen, um die Datenbank direkt mithilfe von "SqlCommand" zu aktualisieren. Beispiel:  

``` csharp
context.Database.SqlCommand(
    "UPDATE dbo.Blogs SET Name = 'Another Name' WHERE BlogId = 1");
```  

Die Entries-Methode für dbupdateconaccesscyexception gibt die dbentityentry-Instanzen für die Entitäten zurück, die nicht aktualisiert werden konnten. (Diese Eigenschaft gibt derzeit immer einen einzelnen Wert für Parallelitäts Probleme zurück. Es können mehrere Werte für allgemeine Update Ausnahmen zurückgegeben werden.) Eine Alternative kann in einigen Situationen darin bestehen, Einträge für alle Entitäten abzurufen, die möglicherweise aus der Datenbank erneut geladen werden müssen, und für jede dieser Elemente den Befehl zum erneuten Laden aufzurufen.  

## <a name="resolving-optimistic-concurrency-exceptions-as-client-wins"></a>Auflösen von Ausnahmen der vollständigen Parallelität als Client gewinnt  

Das obige Beispiel, das das erneute Laden verwendet, wird manchmal als Database WINS oder Store WINS bezeichnet, da die Werte in der Entität durch Werte aus der Datenbank überschrieben werden. Manchmal möchten Sie möglicherweise das Gegenteil durchführen und die Werte in der Datenbank mit den Werten überschreiben, die sich derzeit in der Entität befinden. Dies wird auch als Client WINS bezeichnet und kann durch das erhalten der aktuellen Daten Bank Werte und durch Festlegen der ursprünglichen Werte für die Entität erreicht werden. (Weitere Informationen zu aktuellen und ursprünglichen Werten finden Sie [unter Arbeiten mit Eigenschafts Werten](xref:ef6/saving/change-tracking/property-values) .) Zum Beispiel:  

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

## <a name="custom-resolution-of-optimistic-concurrency-exceptions"></a>Benutzerdefinierte Auflösung von Ausnahmen bei optimistischer Parallelität  

Manchmal möchten Sie möglicherweise die Werte in der Datenbank mit den Werten kombinieren, die derzeit in der Entität gespeichert sind. Dies erfordert in der Regel eine benutzerdefinierte Logik oder eine Benutzerinteraktion. Beispielsweise können Sie dem Benutzer ein Formular mit den aktuellen Werten, den Werten in der Datenbank und einem Standardsatz von aufgelösten Werten präsentieren. Der Benutzer bearbeitet dann die aufgelösten Werte nach Bedarf, und es wären diese aufgelösten Werte, die in der Datenbank gespeichert werden. Dies kann mit den dbpropertyvalues-Objekten erfolgen, die von CurrentValues und getdatabasevalues für den Eintrag der Entität zurückgegeben werden. Beispiel:  

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

## <a name="custom-resolution-of-optimistic-concurrency-exceptions-using-objects"></a>Benutzerdefinierte Auflösung von Ausnahmen mit optimistischer Parallelität mithilfe von Objekten  

Der obige Code verwendet dbpropertyvalues-Instanzen, um aktuelle, Datenbank-und aufgelöste Werte zu übergeben. Manchmal ist es möglicherweise einfacher, Instanzen des Entitäts Typs für dieses zu verwenden. Dies kann mithilfe der Methoden "-Objekt" und "SetValues" von dbpropertyvalues erreicht werden. Beispiel:  

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
