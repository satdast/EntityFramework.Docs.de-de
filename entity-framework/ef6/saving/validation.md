---
title: Validierung-EF6
description: Validierung in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 77d6a095-c0d0-471e-80b9-8f9aea6108b2
ms.openlocfilehash: 0e0f5c581681e7b21b6591ade1541d774e7280bc
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619813"
---
# <a name="data-validation"></a>Datenüberprüfung
> [!NOTE]
> **EF 4.1** und höher: die Features, APIs usw., die auf dieser Seite erläutert wurden, wurden in Entity Framework 4,1 eingeführt. Wenn Sie eine frühere Version verwenden, werden einige oder alle Informationen nicht angewendet.

Der Inhalt auf dieser Seite wird von einem Artikel angepasst, der ursprünglich von Julie Lerman () geschrieben wurde [https://thedatafarm.com](https://thedatafarm.com) .

Entity Framework bietet eine Vielzahl von Validierungs Features, die für die Client seitige Validierung in eine Benutzeroberfläche übertragen oder für die serverseitige Validierung verwendet werden können. Wenn Sie Code First verwenden, können Sie Validierungen mithilfe von Anmerkungen oder überflüssige API-Konfigurationen angeben. Weitere Überprüfungen und komplexere können im Code angegeben werden und funktionieren unabhängig davon, ob Ihr Modell von Code First, Model First oder Database First stammt.

## <a name="the-model"></a>Die Model-Komponente

Ich zeige die Validierungen mit einem einfachen paar von Klassen: Blog und Post.

``` csharp
public class Blog
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string BloggerName { get; set; }
    public DateTime DateCreated { get; set; }
    public virtual ICollection<Post> Posts { get; set; }
}

public class Post
{
    public int Id { get; set; }
    public string Title { get; set; }
    public DateTime DateCreated { get; set; }
    public string Content { get; set; }
    public int BlogId { get; set; }
    public ICollection<Comment> Comments { get; set; }
}
```

## <a name="data-annotations"></a>Datenanmerkungen

Code First verwendet Anmerkungen aus der `System.ComponentModel.DataAnnotations` Assembly als eine Methode zum Konfigurieren von Code First-Klassen. Bei diesen Anmerkungen handelt es sich um solche Anmerkungen, die Regeln wie z `Required` . b., und bereitstellen `MaxLength` `MinLength` . Eine Reihe von .NET-Client Anwendungen erkennt diese Anmerkungen auch, z. b. ASP.NET MVC. Sie können mit diesen Anmerkungen sowohl die Client seitige als auch die serverseitige Validierung erreichen. Beispielsweise können Sie erzwingen, dass die Eigenschaft "Blog Titel" eine erforderliche Eigenschaft ist.

``` csharp
[Required]
public string Title { get; set; }
```

Ohne zusätzlichen Code oder Markup Änderungen in der Anwendung führt eine vorhandene MVC-Anwendung eine Client seitige Validierung durch, wobei eine Nachricht auch dynamisch mit den Namen der Eigenschaft und der Anmerkung erstellt wird.

![Abbildung 1](~/ef6/media/figure01.png)

In der Post Back-Methode dieser CREATE VIEW-Methode wird Entity Framework verwendet, um den neuen Blog in der Datenbank zu speichern. die Client seitige Validierung von MVC wird jedoch ausgelöst, bevor die Anwendung den Code erreicht.

Die Client seitige Validierung ist jedoch nicht Aufzählungs sicher. Benutzer können sich auf die Features Ihres Browsers auswirken oder noch schlimmer, ein Hacker könnte eine gewisse Schwierigkeit verwenden, um die Validierung der Benutzeroberfläche zu vermeiden. Entity Framework erkennt jedoch auch die `Required` -Anmerkung und überprüft sie.

Eine einfache Möglichkeit, dies zu testen, besteht darin, das Client seitige Validierungs Feature von MVC zu deaktivieren. Dies können Sie in der web.config Datei der MVC-Anwendung tun. Der appSettings-Abschnitt enthält einen Schlüssel für clientvalidationaktivierte. Wenn dieser Schlüssel auf false festgelegt wird, kann die Benutzeroberfläche keine Validierungen durchführen.

``` xml
<appSettings>
    <add key="ClientValidationEnabled"value="false"/>
    ...
</appSettings>
```

Auch wenn die Client seitige Validierung deaktiviert ist, erhalten Sie dieselbe Antwort in Ihrer Anwendung. Die Fehlermeldung "das Titelfeld ist erforderlich" wird wie zuvor angezeigt. Mit der Ausnahme, dass es sich nun um das Ergebnis der serverseitigen Validierung handelt. Entity Framework führt die Überprüfung der `Required` Anmerkung durch (bevor es sogar zu einem Fehler führt, einen `INSERT` Befehl zu erstellen, der an die Datenbank gesendet wird), und gibt den Fehler an MVC zurück, der die Meldung anzeigt.

## <a name="fluent-api"></a>Fluent-API

Sie können die fließende API von Code First anstelle von Anmerkungen verwenden, um dieselbe Clientseite & serverseitigen Validierung zu erhalten. Anstatt zu verwenden `Required` , zeige ich Ihnen dies mithilfe einer MaxLength-Validierung.

Fließende API-Konfigurationen werden angewendet, wenn Code First das Modell aus den Klassen aufbaut. Sie können die Konfigurationen einfügen, indem Sie die onmodelcreating-Methode der dbcontext-Klasse überschreiben. Es folgt eine Konfiguration, die angibt, dass die bloggername-Eigenschaft nicht länger als 10 Zeichen sein darf.

``` csharp
public class BlogContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
    public DbSet<Comment> Comments { get; set; }

    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>().Property(p => p.BloggerName).HasMaxLength(10);
    }
}
```

Validierungs Fehler, die auf der Grundlage der überflüssigen API-Konfigurationen ausgelöst werden, erreichen die Benutzeroberfläche nicht automatisch, Sie können Sie jedoch im Code erfassen und entsprechend darauf reagieren.

Im folgenden finden Sie einen Fehlercode für die Ausnahmebehandlung in der blogcontroller-Klasse der Anwendung, der diesen Überprüfungs Fehler erfasst, wenn Entity Framework versucht, einen Blog mit einem Blog Name zu speichern, der das Maximum von 10 Zeichen überschreitet.

``` csharp
[HttpPost]
public ActionResult Edit(int id, Blog blog)
{
    try
    {
        db.Entry(blog).State = EntityState.Modified;
        db.SaveChanges();
        return RedirectToAction("Index");
    }
    catch (DbEntityValidationException ex)
    {
        var error = ex.EntityValidationErrors.First().ValidationErrors.First();
        this.ModelState.AddModelError(error.PropertyName, error.ErrorMessage);
        return View();
    }
}
```

Die Überprüfung wird nicht automatisch an die Ansicht zurückgegeben, weshalb der von verwendete zusätzliche Code `ModelState.AddModelError` verwendet wird. Dadurch wird sichergestellt, dass die Fehlerdetails in der Ansicht angezeigt werden. Anschließend wird der Fehler mit dem `ValidationMessageFor` htmlhelper angezeigt.

``` csharp
@Html.ValidationMessageFor(model => model.BloggerName)
```

## <a name="ivalidatableobject"></a>IValidatableObject

`IValidatableObject` ist eine Schnittstelle, die sich in befindet `System.ComponentModel.DataAnnotations` . Obwohl es nicht Teil der Entity Framework-API ist, können Sie es für die serverseitige Validierung in ihren Entity Framework-Klassen weiterhin nutzen. `IValidatableObject` stellt eine `Validate` Methode bereit, die von Entity Framework während der SaveChanges aufgerufen wird, oder Sie können sich jederzeit selbst anrufen, wenn Sie die Klassen überprüfen möchten.

Konfigurationen wie `Required` und `MaxLength` führen die Validierung für ein einzelnes Feld aus. In der- `Validate` Methode können Sie noch komplexere Logik haben, z. b. zwei Felder vergleichen.

Im folgenden Beispiel wurde die `Blog` -Klasse so erweitert, dass implementiert wird `IValidatableObject` und dann eine Regel bereitgestellt wird, die `Title` und `BloggerName` nicht erfüllen können.

``` csharp
public class Blog : IValidatableObject
{
    public int Id { get; set; }

    [Required]
    public string Title { get; set; }

    public string BloggerName { get; set; }
    public DateTime DateCreated { get; set; }
    public virtual ICollection<Post> Posts { get; set; }

    public IEnumerable<ValidationResult> Validate(ValidationContext validationContext)
    {
        if (Title == BloggerName)
        {
            yield return new ValidationResult(
                "Blog Title cannot match Blogger Name",
                new[] { nameof(Title), nameof(BloggerName) });
        }
    }
}
```

Der `ValidationResult` -Konstruktor nimmt einen `string` , der die Fehlermeldung und ein Array von `string` s darstellt, die die Elementnamen darstellen, die der Validierung zugeordnet sind. Da bei dieser Überprüfung sowohl der `Title` als auch der überprüft `BloggerName` wird, werden beide Eigenschaftsnamen zurückgegeben.

Anders als bei der Überprüfung, die von der reflüssigen API bereitgestellt wird, wird dieses Validierungs Ergebnis von der Sicht erkannt, und der Ausnahmehandler, den ich zuvor zum Hinzufügen des Fehlers verwendet habe, `ModelState` ist unnötig. Da ich beide Eigenschaftsnamen in der festgelegt habe `ValidationResult` , zeigen die MVC-htmlhilfsprogramme die Fehlermeldung für beide Eigenschaften an.

![Abbildung 2](~/ef6/media/figure02.png)

## <a name="dbcontextvalidateentity"></a>Dbcontext. validateentity

`DbContext` verfügt über eine über schreibbare Methode mit dem Namen `ValidateEntity` . Beim Aufrufen `SaveChanges` von wird Entity Framework diese Methode für jede Entität im Cache aufrufen, deren Zustand nicht ist `Unchanged` . Sie können die Validierungs Logik direkt in die-Methode einfügen oder sogar mit dieser Methode aufzurufen, z. b. die-Methode, die `Blog.Validate` im vorherigen Abschnitt hinzugefügt wurde.

Im folgenden finden Sie ein Beispiel für eine `ValidateEntity` außer Kraft setzung, mit der neue s überprüft werden, `Post` um sicherzustellen, dass der Beitrags Titel nicht bereits verwendet wurde. Zuerst wird überprüft, ob es sich bei der Entität um einen Beitrag handelt und ob Ihr Zustand hinzugefügt wurde. Wenn dies der Fall ist, wird in der Datenbank gesucht, ob bereits ein Beitrag mit dem gleichen Titel vorhanden ist. Wenn bereits ein Beitrag vorhanden ist, wird ein neuer `DbEntityValidationResult` erstellt.

`DbEntityValidationResult` enthält einen `DbEntityEntry` und einen `ICollection<DbValidationErrors>` für eine einzelne Entität. Am Anfang dieser Methode `DbEntityValidationResult` wird eine instanziiert, und anschließend werden alle ermittelten Fehler in der-Auflistung hinzugefügt `ValidationErrors` .

``` csharp
protected override DbEntityValidationResult ValidateEntity (
    System.Data.Entity.Infrastructure.DbEntityEntry entityEntry,
    IDictionary<object, object> items)
{
    var result = new DbEntityValidationResult(entityEntry, new List<DbValidationError>());

    if (entityEntry.Entity is Post post && entityEntry.State == EntityState.Added)
    {
        // Check for uniqueness of post title
        if (Posts.Where(p => p.Title == post.Title).Any())
        {
            result.ValidationErrors.Add(
                    new System.Data.Entity.Validation.DbValidationError(
                        nameof(Title),
                        "Post title must be unique."));
        }
    }

    if (result.ValidationErrors.Count > 0)
    {
        return result;
    }
    else
    {
        return base.ValidateEntity(entityEntry, items);
    }
}
```

## <a name="explicitly-triggering-validation"></a>Explizite Auslösen der Validierung

Ein-Aufrufe `SaveChanges` von löst alle in diesem Artikel behandelten Überprüfungen aus. Sie müssen sich jedoch nicht darauf verlassen `SaveChanges` . Möglicherweise möchten Sie an anderer Stelle in der Anwendung überprüfen.

`DbContext.GetValidationErrors` löst alle Überprüfungen, die durch Anmerkungen oder die fließende API definiert werden, die in erstellte Validierung `IValidatableObject` (z. b `Blog.Validate` .) und die in der Methode ausgeführten Überprüfungen aus `DbContext.ValidateEntity` .

Der folgende Code ruft `GetValidationErrors` für die aktuelle Instanz eines auf `DbContext` . `ValidationErrors` sind nach Entitätstyp gruppiert `DbEntityValidationResult` . Der Code durchläuft zuerst durch die `DbEntityValidationResult` von der-Methode zurückgegebenen e und dann durch alle `DbValidationError` innerhalb von.

``` csharp
foreach (var validationResult in db.GetValidationErrors())
{
    foreach (var error in validationResult.ValidationErrors)
    {
        Debug.WriteLine(
            "Entity Property: {0}, Error {1}",
            error.PropertyName,
            error.ErrorMessage);
    }
}
```

## <a name="other-considerations-when-using-validation"></a>Weitere Überlegungen bei der Verwendung der Validierung

Dies sind einige andere Punkte, die bei der Verwendung Entity Framework Validierung zu beachten sind:

- Lazy Load ist während der Überprüfung deaktiviert.
- EF überprüft Daten Anmerkungen in nicht zugeordneten Eigenschaften (Eigenschaften, die keiner Spalte in der Datenbank zugeordnet sind).
- Die Überprüfung wird durchgeführt, nachdem Änderungen während der erkannt wurden `SaveChanges` . Wenn Sie während der Überprüfung Änderungen vornehmen, liegt es in ihrer Verantwortung, die Änderungs Protokollierung zu benachrichtigen.
- `DbUnexpectedValidationException` wird ausgelöst, wenn während der Überprüfung Fehler auftreten.
- Facetten, die Entity Framework im Modell enthalten (maximale Länge, erforderlich usw.), führen zu einer Validierung, auch wenn keine Daten Anmerkungen in ihren Klassen vorhanden sind und/oder Sie den EF-Designer zum Erstellen des Modells verwendet haben.
- Rang Folge Regeln:
  - Fließende API-Aufrufe überschreiben die entsprechenden Daten Anmerkungen.
- Ausführungsreihenfolge:
  - Überprüfung der Eigenschaften vor Typvalidierung
  - Die Typvalidierung tritt nur auf, wenn die Eigenschaften Überprüfung erfolgreich
- Wenn eine Eigenschaft Komplex ist, enthält die Überprüfung auch Folgendes:
  - Überprüfung auf Eigenschafts Ebene für die Eigenschaften komplexer Typen
  - Validierung der Typebene für den komplexen Typ, einschließlich `IValidatableObject` der Validierung für den komplexen Typ

## <a name="summary"></a>Zusammenfassung

Die Überprüfungs-API in Entity Framework wird sehr gut mit der Client seitigen Validierung in MVC abgespielt, aber Sie müssen sich nicht auf die Client seitige Validierung verlassen. Entity Framework kümmert sich um die Überprüfung auf Serverseite für DataAnnotations oder Konfigurationen, die Sie mit der Code First-überflüssigen API angewendet haben.

Außerdem wurde eine Reihe von Erweiterungs Punkten zum Anpassen des Verhaltens angezeigt, unabhängig davon, ob Sie die- `IValidatableObject` Schnittstelle verwenden oder die- `DbContext.ValidateEntity` Methode tippen. Und die beiden letzten Überprüfungs Möglichkeiten sind über das verfügbar, unabhängig davon `DbContext` , ob Sie den Code First, Model First oder Database First Workflow verwenden, um das konzeptionelle Modell zu beschreiben.
