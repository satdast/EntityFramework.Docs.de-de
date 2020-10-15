---
title: Code First Daten Anmerkungen-EF6
description: Code First von Daten Anmerkungen in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/data-annotations
ms.openlocfilehash: f6bf063ab99c5b7ef01993aec8fd6bdd06dd8f7d
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066472"
---
# <a name="code-first-data-annotations"></a>Code First Data Annotations
> [!NOTE]
> **EF 4.1** und höher: die Features, APIs usw., die auf dieser Seite erläutert wurden, wurden in Entity Framework 4,1 eingeführt. Wenn Sie eine frühere Version verwenden, sind einige oder alle dieser Informationen nicht anwendbar.

Der Inhalt auf dieser Seite wird von einem Artikel angepasst, der ursprünglich von Julie Lerman () geschrieben wurde \<http://thedatafarm.com> .

Entity Framework Code First ermöglicht Ihnen die Verwendung ihrer eigenen Domänen Klassen, um das Modell darzustellen, das EF zum Ausführen von Abfragen, Änderungs Nachverfolgung und Aktualisierungs Funktionen verwendet. Code First nutzt ein Programmier Muster, das als "Konvention over Configuration" bezeichnet wird. Code First geht davon aus, dass die Klassen den Konventionen Entity Framework entsprechen und in diesem Fall automatisch herausfinden, wie der Auftrag ausgeführt wird. Wenn Ihre Klassen diesen Konventionen jedoch nicht folgen, können Sie Ihren Klassen Konfigurationen hinzufügen, um EF die erforderlichen Informationen bereitzustellen.

Code First bietet Ihnen zwei Möglichkeiten, diese Konfigurationen ihren Klassen hinzuzufügen. Eine Methode verwendet einfache Attribute namens DataAnnotations, die zweite verwendet die fließende API Code First, die Ihnen eine Möglichkeit bietet, Konfigurationen im Code Imperativ zu beschreiben.

Der Schwerpunkt dieses Artikels liegt auf der Verwendung von DataAnnotations (im Namespace System. ComponentModel. DataAnnotations) zum Konfigurieren der Klassen – Hervorhebung der am häufigsten benötigten Konfigurationen. DataAnnotations werden auch von einer Reihe von .NET-Anwendungen, wie z. b. ASP.NET MVC, interpretiert, sodass diese Anwendungen die gleichen Anmerkungen für Client seitige Überprüfungen nutzen können.


## <a name="the-model"></a>Die Model-Komponente

Ich zeige Ihnen Code First DataAnnotations mit einem einfachen paar von Klassen: Blog und Post.

``` csharp
    public class Blog
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string BloggerName { get; set;}
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

Wie Sie sind, befolgen die Blog-und Post-Klassen die Code First-Konvention und erfordern keine Anpassungen, um die EF-Kompatibilität zu ermöglichen. Allerdings können Sie mit den Anmerkungen auch weitere Informationen zu den Klassen und der Datenbank, denen Sie zugeordnet sind, für EF bereitstellen.

 

## <a name="key"></a>Schlüssel

Entity Framework stützt sich auf jede Entität, die einen Schlüsselwert aufweist, der für die Entitäts Verfolgung verwendet wird. Eine Code First Konvention sind implizite Schlüsseleigenschaften. Code First suchen nach einer Eigenschaft mit dem Namen "ID" oder einer Kombination aus Klassenname und ID, z. b. "BlogId". Diese Eigenschaft wird einer Primärschlüssel Spalte in der Datenbank zugeordnet.

Die Blog-und Post-Klassen befolgen diese Konvention. Was geschieht, wenn Sie nicht? Was passiert, wenn der Blog stattdessen den Namen *primarytrackingkey* oder sogar *foo*verwendet hat? Wenn Code First keine Eigenschaft findet, die dieser Konvention entspricht, löst er eine Ausnahme aus, da die Anforderung des Entity Framework eine Schlüsseleigenschaft aufweisen muss. Sie können die Schlüssel Anmerkung verwenden, um anzugeben, welche Eigenschaft als EntityKey verwendet werden soll.

``` csharp
    public class Blog
    {
        [Key]
        public int PrimaryTrackingKey { get; set; }
        public string Title { get; set; }
        public string BloggerName { get; set;}
        public virtual ICollection<Post> Posts { get; set; }
    }
```

Wenn Sie das Feature zur Datenbankgenerierung von Code First verwenden, verfügt die Tabelle "Blog" über eine Primärschlüssel Spalte mit dem Namen "primarytrackingkey", die standardmäßig auch als Identität definiert ist.

![Blog Tabelle mit Primärschlüssel](~/ef6/media/jj591583-figure01.png)

### <a name="composite-keys"></a>Zusammengesetzte Schlüssel

Entity Framework unterstützt zusammengesetzte Schlüssel-Primärschlüssel, die aus mehr als einer Eigenschaft bestehen. Beispielsweise können Sie über eine Passport-Klasse verfügen, deren Primärschlüssel eine Kombination aus Passport Number und issuingcountry ist.

``` csharp
    public class Passport
    {
        [Key]
        public int PassportNumber { get; set; }
        [Key]
        public string IssuingCountry { get; set; }
        public DateTime Issued { get; set; }
        public DateTime Expires { get; set; }
    }
```

Wenn Sie versuchen, die oben genannte Klasse in Ihrem EF-Modell zu verwenden, ergibt sich Folgendes `InvalidOperationException` :

*Die zusammengesetzte Primärschlüssel Anordnung für den Typ "Passport" kann nicht bestimmt werden. Verwenden Sie die ColumnAttribute-Methode oder die Haskey-Methode, um eine Reihenfolge für zusammengesetzte Primärschlüssel anzugeben.*

Um zusammengesetzte Schlüssel verwenden zu können, müssen Entity Framework eine Reihenfolge für die Schlüsseleigenschaften definieren. Hierfür können Sie mithilfe der Spalten Anmerkung eine Bestellung angeben.

>[!NOTE]
> Der ORDER-Wert ist relativ (nicht Index basiert), sodass alle Werte verwendet werden können. Beispielsweise sind 100 und 200 anstelle von 1 und 2 zulässig.

``` csharp
    public class Passport
    {
        [Key]
        [Column(Order=1)]
        public int PassportNumber { get; set; }
        [Key]
        [Column(Order = 2)]
        public string IssuingCountry { get; set; }
        public DateTime Issued { get; set; }
        public DateTime Expires { get; set; }
    }
```

Wenn Sie über Entitäten mit zusammengesetzten Fremdschlüsseln verfügen, müssen Sie die gleiche Spalten Reihenfolge angeben, die Sie für die entsprechenden Primärschlüssel Eigenschaften verwendet haben.

Nur die relative Reihenfolge in den Fremdschlüssel Eigenschaften muss identisch sein. die genauen Werte, die der **Bestellung** zugewiesen sind, müssen nicht übereinstimmen. In der folgenden Klasse können z. b. 3 und 4 anstelle von 1 und 2 verwendet werden.

``` csharp
    public class PassportStamp
    {
        [Key]
        public int StampId { get; set; }
        public DateTime Stamped { get; set; }
        public string StampingCountry { get; set; }

        [ForeignKey("Passport")]
        [Column(Order = 1)]
        public int PassportNumber { get; set; }

        [ForeignKey("Passport")]
        [Column(Order = 2)]
        public string IssuingCountry { get; set; }

        public Passport Passport { get; set; }
    }
```

## <a name="required"></a>Erforderlich

Die- `Required` Anmerkung weist EF an, dass eine bestimmte Eigenschaft erforderlich ist.

Durch das Hinzufügen von erforderlich für die Title-Eigenschaft wird EF (und MVC) gezwungen, sicherzustellen, dass die Eigenschaft über Daten verfügt.

``` csharp
    [Required]
    public string Title { get; set; }
```

Ohne zusätzlichen Code oder Markup Änderungen in der Anwendung führt eine MVC-Anwendung eine Client seitige Validierung durch, wobei eine Nachricht auch dynamisch mit den Namen der Eigenschaft und der Anmerkung erstellt wird.

![Fehler beim Erstellen einer Seite mit dem Titel "erforderlich".](~/ef6/media/jj591583-figure02.png)

Das erforderliche Attribut wirkt sich auch auf die generierte Datenbank aus, indem die zugeordnete Eigenschaft nicht auf NULL festgelegt werden kann. Beachten Sie, dass sich das Feld "Title" in "not NULL" geändert hat.

>[!NOTE]
> In einigen Fällen ist es möglicherweise nicht möglich, dass die Spalte in der Datenbank keine NULL-Werte zulässt, auch wenn die-Eigenschaft erforderlich ist. Wenn z. b. eine TPH-Vererbungs Strategie verwendet wird, werden Daten für mehrere Typen in einer einzelnen Tabelle gespeichert. Wenn ein abgeleiteter Typ eine erforderliche Eigenschaft enthält, kann die Spalte nicht auf NULL festleg Bare Werte festgelegt werden, da nicht alle Typen in der Hierarchie über diese Eigenschaft verfügen.

 

![Tabelle "Blogs"](~/ef6/media/jj591583-figure03.png)

 

## <a name="maxlength-and-minlength"></a>MaxLength und minLength

`MaxLength`Mit dem-Attribut und dem- `MinLength` Attribut können Sie zusätzliche Eigenschaften Überprüfungen angeben, wie Sie dies durchgeführt haben `Required` .

Im folgenden finden Sie den Blog Name mit Längen Anforderungen. Das Beispiel veranschaulicht auch das Kombinieren von Attributen.

``` csharp
    [MaxLength(10),MinLength(5)]
    public string BloggerName { get; set; }
```

Die MaxLength-Anmerkung wirkt sich auf die Datenbank aus, indem die Länge der Eigenschaft auf 10 festgelegt wird.

![Tabelle "Blogs" mit der maximalen Länge für die bloggername-Spalte](~/ef6/media/jj591583-figure04.png)

Bei der Client seitigen MVC-Anmerkung und der serverseitigen EF 4,1-Anmerkung wird diese Validierung berücksichtigt, und es wird erneut dynamisch eine Fehlermeldung erstellt: "das Feld" bloggername "muss eine Zeichenfolge oder ein Arraytyp mit einer maximalen Länge von" 10 "sein. Diese Nachricht ist ein wenig lang. Viele Anmerkungen ermöglichen es Ihnen, eine Fehlermeldung mit dem ErrorMessage-Attribut anzugeben.

``` csharp
    [MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```

Sie können auch ErrorMessage in der erforderlichen Anmerkung angeben.

![Seite mit benutzerdefinierter Fehlermeldung erstellen](~/ef6/media/jj591583-figure05.png)

 

## <a name="notmapped"></a>Nicht zugeordnet

Die Code First-Konvention legt fest, dass jede Eigenschaft mit einem unterstützten Datentyp in der Datenbank dargestellt wird. Dies ist jedoch nicht immer der Fall in Ihren Anwendungen. Beispielsweise könnten Sie über eine Eigenschaft in der Blog-Klasse verfügen, die einen Code auf Grundlage der Felder Titel und bloggername erstellt. Diese Eigenschaft kann dynamisch erstellt werden und muss nicht gespeichert werden. Sie können alle Eigenschaften, die nicht der Datenbank zugeordnet sind, mit der Anmerkung "notmapping" markieren, z. b. diese blogcode-Eigenschaft.

``` csharp
    [NotMapped]
    public string BlogCode
    {
        get
        {
            return Title.Substring(0, 1) + ":" + BloggerName.Substring(0, 1);
        }
    }
```

 

## <a name="complextype"></a>ComplexType

Es ist nicht ungewöhnlich, dass Sie Ihre Domänen Entitäten über einen Satz von Klassen hinweg beschreiben und dann diese Klassen auf die Beschreibung einer kompletten Entität umschichten. Beispielsweise können Sie dem Modell eine Klasse mit dem Namen blogdetails hinzufügen.

``` csharp
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
```

Beachten Sie, dass keinen `BlogDetails` Typ von Schlüsseleigenschaft hat. Im Domänen gestützten Design `BlogDetails` wird als Wertobjekt bezeichnet. Entity Framework bezieht sich auf Wert Objekte als komplexe Typen.Komplexe Typen können nicht selbst nachverfolgt werden.

Als Eigenschaft in der- `Blog` Klasse wird jedoch `BlogDetails` als Teil eines-Objekts nachverfolgt `Blog` . Damit Code zuerst dies erkennt, müssen Sie die `BlogDetails` Klasse als markieren `ComplexType` .

``` csharp
    [ComplexType]
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
```

Nun können Sie in der-Klasse eine Eigenschaft hinzufügen `Blog` , die `BlogDetails` für diesen Blog darstellt.

``` csharp
        public BlogDetails BlogDetail { get; set; }
```

In der-Datenbank `Blog` enthält die Tabelle alle Eigenschaften des Blogs einschließlich der Eigenschaften, die in der-Eigenschaft enthalten sind `BlogDetail` . Standardmäßig wird jedem der Name des komplexen Typs "blogdetail" vorangestellt.

![Blog Tabelle mit komplexem Typ](~/ef6/media/jj591583-figure06.png)


## <a name="concurrencycheck"></a>ConcurrencyCheck

Mit der-Anmerkung `ConcurrencyCheck` können Sie eine oder mehrere Eigenschaften markieren, die für die Parallelitäts Überprüfung in der Datenbank verwendet werden sollen, wenn ein Benutzer eine Entität bearbeitet oder löscht. Wenn Sie mit dem EF-Designer gearbeitet haben, entspricht dies dem Festlegen der Eigenschaft `ConcurrencyMode` auf `Fixed` .

Sehen wir uns `ConcurrencyCheck` an, wie funktioniert, indem es der-Eigenschaft hinzugefügt wird `BloggerName` .

``` csharp
    [ConcurrencyCheck, MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```

Wenn `SaveChanges` aufgerufen wird, `ConcurrencyCheck` `BloggerName` wird der ursprüngliche Wert dieser Eigenschaft in der Aktualisierung verwendet, aufgrund der-Anmerkung für das Feld. Der Befehl versucht, die richtige Zeile zu suchen, indem nicht nur der Schlüsselwert, sondern auch der ursprüngliche Wert von gefiltert wird `BloggerName` .Hier sind die wichtigen Teile des Update-Befehls, die an die Datenbank gesendet werden. in diesem Fall können Sie sehen, dass der Befehl die Zeile mit dem `PrimaryTrackingKey` Wert "1" und `BloggerName` "Julie" aktualisiert. Dies war der ursprüngliche Wert, als dieser Blog aus der Datenbank abgerufen wurde.

``` SQL
    where (([PrimaryTrackingKey] = @4) and ([BloggerName] = @5))
    @4=1,@5=N'Julie'
```

Wenn jemand den Blogger-Namen für diesen Blog in der Zwischenzeit geändert hat, schlägt dieses Update fehl, und Sie erhalten eine **dbupdatecon-cyexception** , die Sie behandeln müssen.

 

## <a name="timestamp"></a>TimeStamp

Es ist häufiger, rowversion-oder Zeitstempel-Felder für die Parallelitäts Überprüfung zu verwenden. Anstatt die-Anmerkung `ConcurrencyCheck` zu verwenden, können Sie jedoch die spezifischere Anmerkung verwenden, solange `TimeStamp` der Typ der Eigenschaft ein Bytearray ist. Code First behandelt `Timestamp` Eigenschaften wie `ConcurrencyCheck` Eigenschaften, aber es wird auch sichergestellt, dass das Datenbankfeld, das von Code First generiert wird, keine NULL-Werte zulässt. In einer bestimmten Klasse kann nur eine Zeitstempel-Eigenschaft vorhanden sein.

Fügen Sie der Blog-Klasse die folgende Eigenschaft hinzu:

``` csharp
    [Timestamp]
    public Byte[] TimeStamp { get; set; }
```

führt dazu, dass Code zuerst in der Datenbanktabelle eine Zeitstempel-Spalte erstellt, die keine NULL-Werte zulässt.

![Tabelle "Blogs" mit Zeitstempel Spalte](~/ef6/media/jj591583-figure07.png)

 

## <a name="table-and-column"></a>Tabelle und Spalte

Wenn Sie Code First die Datenbank erstellen möchten, können Sie den Namen der zu erstellenden Tabellen und Spalten ändern. Sie können auch Code First mit einer vorhandenen Datenbank verwenden. Es ist jedoch nicht immer der Fall, dass die Namen der Klassen und Eigenschaften in der Domäne den Namen der Tabellen und Spalten in der Datenbank entsprechen.

Meine Klasse wird benannt `Blog` , und Code First geht davon aus, dass dies einer Tabelle mit dem Namen zugeordnet wird `Blogs` . Wenn dies nicht der Fall ist, können Sie den Namen der Tabelle mit dem- `Table` Attribut angeben. In diesem Beispiel gibt die-Anmerkung an, dass der Tabellenname **internalblogs**ist.

``` csharp
    [Table("InternalBlogs")]
    public class Blog
```

Die-Anmerkung `Column` ist eine bessere Angabe der Attribute einer zugeordneten Spalte. Sie können einen Namen, einen Datentyp oder sogar die Reihenfolge festlegen, in der eine Spalte in der Tabelle angezeigt wird. Im folgenden finden Sie ein Beispiel für das- `Column` Attribut.

``` csharp
    [Column("BlogDescription", TypeName="ntext")]
    public String Description {get;set;}
```

Verwechseln Sie `TypeName` das Attribut der Spalte nicht mit dem DataType-DataAnnotation. DataType ist eine Anmerkung, die für die Benutzeroberfläche verwendet wird, und wird von Code First ignoriert.

Dies ist die Tabelle, nachdem Sie neu generiert wurde. Der Tabellenname wurde in **internalblogs** geändert, und `Description` die Spalte aus dem komplexen Typ lautet jetzt `BlogDescription` . Da der Name in der-Anmerkung angegeben wurde, verwendet Code First nicht die Konvention zum Starten des Spaltennamens mit dem Namen des komplexen Typs.

![Umbenannte Blogs-Tabelle und-Spalte](~/ef6/media/jj591583-figure08.png)

 

## <a name="databasegenerated"></a>Databasegenerated

Wichtige Datenbankfunktionen sind die Möglichkeit, berechnete Eigenschaften zu haben. Wenn Sie die Code First Klassen Tabellen zuordnet, die berechnete Spalten enthalten, möchten Sie Entity Framework nicht versuchen, diese Spalten zu aktualisieren. Sie möchten jedoch, dass EF diese Werte aus der Datenbank zurückgibt, nachdem Sie Daten eingefügt oder aktualisiert haben. Mit der-Anmerkung können Sie `DatabaseGenerated` diese Eigenschaften in der-Klasse zusammen mit der-Enumeration markieren `Computed` . Andere Aufstände sind `None` und `Identity` .

``` csharp
    [DatabaseGenerated(DatabaseGeneratedOption.Computed)]
    public DateTime DateCreated { get; set; }
```

Sie können eine Datenbank verwenden, die für Byte-oder Zeitstempel-Spalten generiert wird, wenn Code First die Datenbank generiert. andernfalls sollten Sie diese nur verwenden, wenn Sie auf vorhandene Datenbanken verweisen, da Code First die Formel für die berechnete Spalte nicht ermitteln kann.

Sie haben darüber hinaus gelesen, dass eine Schlüsseleigenschaft, die eine ganze Zahl ist, standardmäßig ein Identitätsschlüssel in der Datenbank wird. Das wäre dasselbe wie das Festlegen `DatabaseGenerated` von auf `DatabaseGeneratedOption.Identity` . Wenn Sie nicht möchten, dass es sich um einen Identitätsschlüssel handelt, können Sie den Wert auf festlegen `DatabaseGeneratedOption.None` .

 

## <a name="index"></a>Index

> [!NOTE]
> **Nur EF 6.1** : das `Index` Attribut wurde in Entity Framework 6,1 eingeführt. Wenn Sie eine frühere Version verwenden, gelten die Informationen in diesem Abschnitt nicht.

Mithilfe von **Indexattribute**können Sie einen Index für eine oder mehrere Spalten erstellen. Das Hinzufügen des-Attributs zu einer oder mehreren Eigenschaften bewirkt, dass EF den entsprechenden Index in der Datenbank erstellt, wenn die Datenbank erstellt wird, oder ein Gerüst für die entsprechenden Aufrufe von " **anateindex** " erstellt, wenn Sie Code First-Migrationen verwenden.

Der folgende Code führt z. b. dazu, dass ein Index für die- `Rating` Spalte der- `Posts` Tabelle in der-Datenbank erstellt wird.

``` csharp
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }
        [Index]
        public int Rating { get; set; }
        public int BlogId { get; set; }
    }
```

Standardmäßig erhält der Index den Namen " **IX \_ &lt; Property Name &gt; ** " (IX- \_ Bewertung im obigen Beispiel). Sie können jedoch auch einen Namen für den Index angeben. Im folgenden Beispiel wird angegeben, dass der Index benannt werden soll `PostRatingIndex` .

``` csharp
    [Index("PostRatingIndex")]
    public int Rating { get; set; }
```

Standardmäßig sind Indizes nicht eindeutig, Sie können jedoch den `IsUnique` benannten Parameter verwenden, um anzugeben, dass ein Index eindeutig sein muss. Im folgenden Beispiel wird ein eindeutiger Index für `User` den Anmelde Namen eines s eingeführt.

``` csharp
    public class User
    {
        public int UserId { get; set; }

        [Index(IsUnique = true)]
        [StringLength(200)]
        public string Username { get; set; }

        public string DisplayName { get; set; }
    }
```

### <a name="multiple-column-indexes"></a>Multiple-Column Indizes

Indizes, die sich über mehrere Spalten erstrecken, werden mit dem gleichen Namen in mehreren Index Anmerkungen für eine bestimmte Tabelle angegeben. Wenn Sie mehrspaltige Indizes erstellen, müssen Sie eine Reihenfolge für die Spalten im Index angeben. Der folgende Code erstellt z. b. einen mehrspaltigen Index für `Rating` und den `BlogId` Namen " **IX \_ blogidandrating**". `BlogId` ist die erste Spalte im Index und `Rating` ist die zweite.

``` csharp
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }
        [Index("IX_BlogIdAndRating", 2)]
        public int Rating { get; set; }
        [Index("IX_BlogIdAndRating", 1)]
        public int BlogId { get; set; }
    }
```

 

## <a name="relationship-attributes-inverseproperty-and-foreignkey"></a>Beziehungs Attribute: invergenproperty und Fremdschlüssel

> [!NOTE]
> Diese Seite enthält Informationen zum Einrichten von Beziehungen in Ihrem Code First-Modell mithilfe von Daten Anmerkungen. Allgemeine Informationen zu Beziehungen in EF und zum Zugreifen auf und Bearbeiten von Daten mithilfe von Beziehungen finden Sie unter [Beziehungen & Navigations Eigenschaften](xref:ef6/fundamentals/relationships). *

Die Code First-Konvention kümmert sich um die gängigsten Beziehungen in Ihrem Modell. es gibt jedoch einige Fälle, in denen Sie Hilfe benötigen.

Wenn Sie den Namen der Schlüsseleigenschaft in der `Blog` Klasse ändern, wurde ein Problem mit der Beziehung zu hergestellt `Post` . 

Beim Erstellen der Datenbank wird von Code zuerst die `BlogId` -Eigenschaft in der Post-Klasse angezeigt, und Sie wird von der Konvention erkannt, dass Sie mit einem Klassennamen Plus **ID**als Fremdschlüssel für die-Klasse übereinstimmt `Blog` . In der Blog-Klasse gibt es jedoch keine- `BlogId` Eigenschaft. Die Lösung hierfür ist das Erstellen einer Navigations Eigenschaft im `Post` und die Verwendung der `ForeignKey` DataAnnotation, um Code zuerst zu helfen, zu verstehen, wie die Beziehung zwischen den beiden Klassen erstellt wird (mithilfe der- `Post.BlogId` Eigenschaft) und wie Einschränkungen in der Datenbank angegeben werden.

``` csharp
    public class Post
    {
            public int Id { get; set; }
            public string Title { get; set; }
            public DateTime DateCreated { get; set; }
            public string Content { get; set; }
            public int BlogId { get; set; }
            [ForeignKey("BlogId")]
            public Blog Blog { get; set; }
            public ICollection<Comment> Comments { get; set; }
    }
```

Die-Einschränkung in der-Datenbank zeigt eine Beziehung zwischen `InternalBlogs.PrimaryTrackingKey` und `Posts.BlogId` . 

![Beziehung zwischen internalblogs. primarytrackingkey und Posts. BlogId](~/ef6/media/jj591583-figure09.png)

`InverseProperty`Wird verwendet, wenn mehrere Beziehungen zwischen Klassen vorhanden sind.

In der- `Post` Klasse sollten Sie nachverfolgen können, wer einen Blogbeitrag geschrieben hat und wer ihn bearbeitet hat. Hier sind zwei neue Navigations Eigenschaften für die Post-Klasse.

``` csharp
    public Person CreatedBy { get; set; }
    public Person UpdatedBy { get; set; }
```

Sie müssen auch in der-Klasse hinzufügen, auf die `Person` von diesen Eigenschaften verwiesen wird. Die `Person` -Klasse verfügt über Navigations Eigenschaften zurück zum `Post` , eine für alle von der Person geschriebenen Beiträge und eine für alle von dieser Person aktualisierten Beiträge.

``` csharp
    public class Person
    {
            public int Id { get; set; }
            public string Name { get; set; }
            public List<Post> PostsWritten { get; set; }
            public List<Post> PostsUpdated { get; set; }
    }
```

Code First kann die Eigenschaften in den beiden Klassen nicht alleine zuordnen. Die Datenbanktabelle für `Posts` sollte über einen Fremdschlüssel für die `CreatedBy` Person und einen für die `UpdatedBy` Person verfügen, aber Code First erstellt vier Fremdschlüssel Eigenschaften: **Person \_ ID**, **Person \_ Id1**, **kreatedby \_ ID** und **updatedby \_ ID**.

![Postet eine Tabelle mit zusätzlichen Fremdschlüsseln.](~/ef6/media/jj591583-figure10.png)

Um diese Probleme zu beheben, können Sie die-Anmerkung verwenden, `InverseProperty` um die Ausrichtung der Eigenschaften anzugeben.

``` csharp
    [InverseProperty("CreatedBy")]
    public List<Post> PostsWritten { get; set; }

    [InverseProperty("UpdatedBy")]
    public List<Post> PostsUpdated { get; set; }
```

Da die- `PostsWritten` Eigenschaft in Person weiß, dass dies auf den- `Post` Typ verweist, wird die Beziehung zu erstellt `Post.CreatedBy` . Ebenso `PostsUpdated` wird eine Verbindung mit hergestellt `Post.UpdatedBy` . Und Code First erstellt nicht die zusätzlichen Fremdschlüssel.

![Postet Tabelle ohne zusätzliche Fremdschlüssel](~/ef6/media/jj591583-figure11.png)

 

## <a name="summary"></a>Zusammenfassung

Mit DataAnnotations können Sie nicht nur die Client-und serverseitige Validierung in Ihren Code First-Klassen beschreiben, sondern Sie können auch die Annahmen, die Code First über Ihre Klassen auf der Grundlage seiner Konventionen treffen wird, verbessern und sogar korrigieren. Mit DataAnnotations können Sie nicht nur die Generierung von Datenbankschemas steuern, sondern auch die Code First-Klassen einer bereits vorhandenen Datenbank zuordnen.

Obwohl Sie sehr flexibel sind, beachten Sie, dass DataAnnotations nur die am häufigsten benötigten Konfigurationsänderungen bereitstellen, die Sie an Ihren Code First-Klassen vornehmen können. Um die Klassen für einige der edgefälle zu konfigurieren, sollten Sie sich den alternativen Konfigurations Mechanismus Code First der fließend-API ansehen.
