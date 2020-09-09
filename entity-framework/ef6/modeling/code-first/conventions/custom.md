---
title: Benutzerdefinierte Code First Konventionen-EF6
description: Benutzerdefinierte Code First Konventionen in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: dd2bdbd9-ae9e-470a-aeb8-d0ba160499b7
uid: ef6/modeling/code-first/conventions/custom
ms.openlocfilehash: 69e4b0111394e83195f5c0a81624c7b9e45bda52
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617264"
---
# <a name="custom-code-first-conventions"></a>Benutzerdefinierte Code First Konventionen
> [!NOTE]
> **Nur EF6 und höher:** Die Features, APIs usw., die auf dieser Seite erläutert werden, wurden in Entity Framework 6 eingeführt. Wenn Sie eine frühere Version verwenden, gelten manche Informationen nicht.

Wenn Sie Code First verwenden, wird das Modell aus ihren Klassen mithilfe eines Satzes von Konventionen berechnet. Die standardmäßigen [Code First Konventionen](xref:ef6/modeling/code-first/conventions/built-in) bestimmen Dinge, wie z. b. welche Eigenschaft zum Primärschlüssel einer Entität wird, den Namen der Tabelle, der eine Entität zugeordnet ist, und welche Genauigkeit und Dezimalstellen eine Dezimal Spalte Standardmäßig besitzt.

Manchmal sind diese Standard Konventionen nicht ideal für Ihr Modell, und Sie müssen Sie umgehen, indem Sie viele einzelne Entitäten mithilfe von Daten Anmerkungen oder der flüssigen API konfigurieren. Mit benutzerdefinierten Code First Konventionen können Sie eigene Konventionen definieren, die Konfigurations Standardwerte für das Modell bereitstellen. In dieser exemplarischen Vorgehensweise werden die unterschiedlichen Typen von benutzerdefinierten Konventionen erläutert und erläutert, wie diese erstellt werden.


## <a name="model-based-conventions"></a>Modellbasierte Konventionen

Auf dieser Seite wird die dbmodelbuilder-API für benutzerdefinierte Konventionen behandelt. Diese API sollte für die Erstellung der meisten benutzerdefinierten Konventionen ausreichen. Es gibt jedoch auch die Möglichkeit, modellbasierte Konventionen zu verfassen, die das endgültige Modell nach der Erstellung verändern, um erweiterte Szenarien zu verarbeiten. Weitere Informationen finden Sie unter [modellbasierte Konventionen](xref:ef6/modeling/code-first/conventions/model).

 

## <a name="our-model"></a>Unser Modell

Zunächst definieren wir ein einfaches Modell, das wir mit unseren Konventionen verwenden können. Fügen Sie dem Projekt die folgenden Klassen hinzu.

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Data.Entity;
    using System.Linq;

    public class ProductContext : DbContext
    {
        static ProductContext()
        {
            Database.SetInitializer(new DropCreateDatabaseIfModelChanges<ProductContext>());
        }

        public DbSet<Product> Products { get; set; }
    }

    public class Product
    {
        public int Key { get; set; }
        public string Name { get; set; }
        public decimal? Price { get; set; }
        public DateTime? ReleaseDate { get; set; }
        public ProductCategory Category { get; set; }
    }

    public class ProductCategory
    {
        public int Key { get; set; }
        public string Name { get; set; }
        public List<Product> Products { get; set; }
    }
```

 

## <a name="introducing-custom-conventions"></a>Einführung in benutzerdefinierte Konventionen

Wir schreiben eine Konvention, die jede Eigenschaft mit dem Namen Key als Primärschlüssel für den Entitätstyp konfiguriert.

Konventionen sind für den Modell-Generator aktiviert, auf den durch das Überschreiben von onmodelcreating im Kontext zugegriffen werden kann. Aktualisieren Sie die productcontext-Klasse wie folgt:

``` csharp
    public class ProductContext : DbContext
    {
        static ProductContext()
        {
            Database.SetInitializer(new DropCreateDatabaseIfModelChanges<ProductContext>());
        }

        public DbSet<Product> Products { get; set; }

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
            modelBuilder.Properties()
                        .Where(p => p.Name == "Key")
                        .Configure(p => p.IsKey());
        }
    }
```

Nun wird jede Eigenschaft in unserem Modell mit dem Namen Key als Primärschlüssel einer beliebigen Entität konfiguriert, von der Sie gehört.

Wir könnten unsere Konventionen auch spezifischer gestalten, indem wir nach dem Typ der Eigenschaft filtern, die wir konfigurieren werden:

``` csharp
    modelBuilder.Properties<int>()
                .Where(p => p.Name == "Key")
                .Configure(p => p.IsKey());
```

Dadurch werden alle Eigenschaften, die als "Key" bezeichnet werden, als Primärschlüssel der Entität konfiguriert, aber nur, wenn es sich um eine ganze Zahl handelt.

Ein interessantes Feature der IsKey-Methode ist, dass es Additiv ist. Dies bedeutet, wenn Sie "IsKey" für mehrere Eigenschaften anrufen und alle Teil eines zusammengesetzten Schlüssels werden. Der einzige Nachteil hierfür ist, dass Sie beim Angeben mehrerer Eigenschaften für einen Schlüssel auch eine Bestellung für diese Eigenschaften angeben müssen. Dies können Sie erreichen, indem Sie die hascolumnorder-Methode wie folgt aufrufen:

``` csharp
    modelBuilder.Properties<int>()
                .Where(x => x.Name == "Key")
                .Configure(x => x.IsKey().HasColumnOrder(1));

    modelBuilder.Properties()
                .Where(x => x.Name == "Name")
                .Configure(x => x.IsKey().HasColumnOrder(2));
```

Dieser Code konfiguriert die Typen in unserem Modell so, dass Sie über einen zusammengesetzten Schlüssel verfügen, der aus der int-Schlüssel Spalte und der Zeichen folgen Name-Spalte besteht. Wenn das Modell im Designer angezeigt wird, sieht es wie folgt aus:

![zusammengesetzter Schlüssel](~/ef6/media/compositekey.png)

Ein weiteres Beispiel für Eigenschafts Konventionen besteht darin, alle DateTime-Eigenschaften in meinem Modell so zu konfigurieren, dass Sie dem datetime2-Typ in SQL Server anstelle von DateTime zugeordnet werden. Dies kann mit folgendem erreicht werden:

``` csharp
    modelBuilder.Properties<DateTime>()
                .Configure(c => c.HasColumnType("datetime2"));
```

 

## <a name="convention-classes"></a>Konventionen-Klassen

Eine andere Möglichkeit zum Definieren von Konventionen besteht darin, eine Konvention-Klasse zu verwenden, um Ihre Konvention zu kapseln. Wenn Sie eine Konventionen-Klasse verwenden, erstellen Sie einen Typ, der von der Konvention-Klasse im Namespace System. Data. Entity. modelconfiguration. Conventions erbt.

Wir können eine Konvention-Klasse mit der datetime2-Konvention erstellen, die wir zuvor gezeigt haben, indem wir die folgenden Schritte durchführen:

``` csharp
    public class DateTime2Convention : Convention
    {
        public DateTime2Convention()
        {
            this.Properties<DateTime>()
                .Configure(c => c.HasColumnType("datetime2"));        
        }
    }
```

Damit EF diese Konvention verwenden kann, fügen Sie es der Conventions-Auflistung in onmodelcreating hinzu. Wenn Sie die exemplarische Vorgehensweise verwendet haben, sieht die exemplarische Vorgehensweise wie folgt aus:

``` csharp
    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Properties<int>()
                    .Where(p => p.Name.EndsWith("Key"))
                    .Configure(p => p.IsKey());

        modelBuilder.Conventions.Add(new DateTime2Convention());
    }
```

Wie Sie sehen können, fügen wir der Konventionen-Auflistung eine Instanz der Konvention hinzu. Die Vererbung von der Konvention stellt eine bequeme Möglichkeit dar, Konventionen zwischen Teams und Projekten zu gruppieren und zu teilen. Sie könnten z. b. über eine Klassenbibliothek mit einem gemeinsamen Satz von Konventionen verfügen, die von all ihren Organisationen verwendet werden.

 

## <a name="custom-attributes"></a>Benutzerdefinierte Attribute

Eine weitere großartige Verwendung von Konventionen besteht darin, neue Attribute für die Konfiguration eines Modells zu aktivieren. Um dies zu veranschaulichen, erstellen wir ein Attribut, das zum Markieren von Zeichen folgen Eigenschaften als nicht-Unicode verwendet werden kann.

``` csharp
    [AttributeUsage(AttributeTargets.Property, AllowMultiple = false)]
    public class NonUnicode : Attribute
    {
    }
```

Nun erstellen wir eine Konvention zum Anwenden dieses Attributs auf unser Modell:

``` csharp
    modelBuilder.Properties()
                .Where(x => x.GetCustomAttributes(false).OfType<NonUnicode>().Any())
                .Configure(c => c.IsUnicode(false));
```

Mit dieser Konvention können wir das nonUnicode-Attribut zu einer unserer Zeichen folgen Eigenschaften hinzufügen. Dies bedeutet, dass die Spalte in der Datenbank als VARCHAR anstelle von nvarchar gespeichert wird.

Diese Konvention sollte beachtet werden, wenn Sie das nicht-Unicode-Attribut nicht auf eine Zeichen folgen Eigenschaft setzen, sondern eine Ausnahme auslöst. Dies liegt daran, dass isunicode nicht für einen anderen Typ als eine Zeichenfolge konfiguriert werden kann. Wenn dies der Fall ist, können Sie Ihre Konvention spezifischer gestalten, damit alles, was keine Zeichenfolge ist, herausgefiltert wird.

Obwohl die obige Konvention zum Definieren von benutzerdefinierten Attributen funktioniert, gibt es eine andere API, die viel einfacher zu verwenden ist, insbesondere, wenn Sie Eigenschaften aus der Attribut Klasse verwenden möchten.

In diesem Beispiel aktualisieren wir das Attribut und ändern es in ein isunicode-Attribut. Daher sieht es wie folgt aus:

``` csharp
    [AttributeUsage(AttributeTargets.Property, AllowMultiple = false)]
    internal class IsUnicode : Attribute
    {
        public bool Unicode { get; set; }

        public IsUnicode(bool isUnicode)
        {
            Unicode = isUnicode;
        }
    }
```

Nachdem dies geschehen ist, können wir einen booleschen Wert für unser Attribut festlegen, um der Konvention mitzuteilen, ob eine Eigenschaft Unicode sein soll. Wir könnten dies in der Konvention durchführen, indem wir wie folgt auf die clrproperty der Konfigurations Klasse zugreifen:

``` csharp
    modelBuilder.Properties()
                .Where(x => x.GetCustomAttributes(false).OfType<IsUnicode>().Any())
                .Configure(c => c.IsUnicode(c.ClrPropertyInfo.GetCustomAttribute<IsUnicode>().Unicode));
```

Dies ist leicht genug, aber es gibt eine kompaktere Möglichkeit, dies mit der-Methode der Conventions-API zu erreichen. Die "has"-Methode hat einen Parameter vom Typ "Func &lt; PropertyInfo", "T", der &gt; PropertyInfo akzeptiert, identisch mit der Where-Methode, erwartet jedoch, dass ein-Objekt zurückgegeben wird. Wenn das zurückgegebene Objekt NULL ist, wird die Eigenschaft nicht konfiguriert, d. h., Sie können Eigenschaften wie in der Where-Methode filtern, aber Sie unterscheiden sich darin, dass auch das zurückgegebene Objekt erfasst und an die Configure-Methode übergeben wird. Dies funktioniert wie folgt:

``` csharp
    modelBuilder.Properties()
                .Having(x =>x.GetCustomAttributes(false).OfType<IsUnicode>().FirstOrDefault())
                .Configure((config, att) => config.IsUnicode(att.Unicode));
```

Benutzerdefinierte Attribute sind nicht der einzige Grund für die Verwendung der Methode, die Sie bei der Konfiguration ihrer Typen oder Eigenschaften durch Filtern müssen.

 

## <a name="configuring-types"></a>Konfigurieren von Typen

Bisher waren alle Konventionen für Eigenschaften vorgesehen, aber es gibt einen weiteren Bereich der Konventionen-API für die Konfiguration der Typen in Ihrem Modell. Die Erfahrung ähnelt den Konventionen, die bisher aufgetreten sind, aber die Optionen in configure befinden sich in der Entität anstelle der Eigenschafts Ebene.

Eine der Dinge, mit denen typebenenkonventionen sehr nützlich sein können, ist die Änderung der Benennungs Konvention für Tabellen, entweder für die Zuordnung zu einem vorhandenen Schema, das von EF default abweicht, oder um eine neue Datenbank mit einer anderen Benennungs Konvention zu erstellen. Zu diesem Zweck benötigen wir zuerst eine Methode, die die TypeInfo für einen Typ in unserem Modell akzeptieren kann und den Tabellennamen für diesen Typ zurückgibt:

``` csharp
    private string GetTableName(Type type)
    {
        var result = Regex.Replace(type.Name, ".[A-Z]", m => m.Value[0] + "_" + m.Value[1]);

        return result.ToLower();
    }
```

Diese Methode nimmt einen-Typ an und gibt eine Zeichenfolge zurück, die Kleinbuchstaben mit unterstrichen anstelle von "CamelCase" verwendet. In unserem Modell bedeutet dies, dass die ProductCategory-Klasse der Tabelle Product \_ Category anstelle von productcategories zugeordnet wird.

Sobald wir diese Methode haben, können wir Sie in einer Konvention wie der folgenden nennen:

``` csharp
    modelBuilder.Types()
                .Configure(c => c.ToTable(GetTableName(c.ClrType)));
```

Diese Konvention konfiguriert jeden Typ in unserem Modell so, dass er dem Tabellennamen zugeordnet wird, der von der GetTableName-Methode zurückgegeben wird. Diese Konvention entspricht dem Aufrufen der ToTable-Methode für jede Entität im Modell mithilfe der flüssigen API.

Beachten Sie hierbei Folgendes: Wenn Sie "ToTable" verwenden, wird die Zeichenfolge, die Sie als exakten Tabellennamen angeben, ohne die Pluralisierung übernommen, die normalerweise beim Bestimmen von Tabellennamen verwendet wird. Aus diesem Grund ist der Tabellenname aus unserer Konvention die Produkt \_ Kategorie anstelle von Produkt \_ Kategorien. Wir können dies in unserer Konvention auflösen, indem wir den pluralisierungs Dienst selbst anrufen.

Im folgenden Code verwenden wir die Funktion zur [Abhängigkeitsauflösung](xref:ef6/fundamentals/configuring/dependency-resolution) , die in EF6 hinzugefügt wird, um den pluralisierungs Dienst abzurufen, den EF verwendet hätte, und den Tabellennamen zu pluralisieren.

``` csharp
    private string GetTableName(Type type)
    {
        var pluralizationService = DbConfiguration.DependencyResolver.GetService<IPluralizationService>();

        var result = pluralizationService.Pluralize(type.Name);

        result = Regex.Replace(result, ".[A-Z]", m => m.Value[0] + "_" + m.Value[1]);

        return result.ToLower();
    }
```

> [!NOTE]
> Bei der generischen Version von GetService handelt es sich um eine Erweiterungsmethode im System. Data. Entity. Infrastructure. dependencyresolution-Namespace. Sie müssen ihrem Kontext eine using-Anweisung hinzufügen, um Sie zu verwenden.

### <a name="totable-and-inheritance"></a>Zu Tabelle und Vererbung

Ein weiterer wichtiger Aspekt bei der Inhalts Zuordnung besteht darin, dass Sie die von EF verwendete Zuordnungs Strategie ändern können, wenn Sie einen Typ explizit einer bestimmten Tabelle zuordnen. Wenn Sie für jeden Typ in einer Vererbungs Hierarchie "detable" aufzurufen und den Typnamen wie oben beschrieben als Namen der Tabelle übergeben, ändern Sie die standardmäßige Tabelle pro Hierarchie (TPH)-Mapping-Strategie in "Tabelle pro Typ" (TPT). Die beste Möglichkeit, dies zu beschreiben, ist ein konkretes Beispiel:

``` csharp
    public class Employee
    {
        public int Id { get; set; }
        public string Name { get; set; }
    }

    public class Manager : Employee
    {
        public string SectionManaged { get; set; }
    }
```

Standardmäßig werden sowohl der Employee als auch der Manager derselben Tabelle (Employees) in der Datenbank zugeordnet. Die Tabelle enthält sowohl Mitarbeiter als auch Manager mit einer diskriminatorspalte, die Aufschluss darüber gibt, welcher Instanztyp in den einzelnen Zeilen gespeichert wird. Dies ist die TPH-Zuordnung, da eine einzelne Tabelle für die Hierarchie vorhanden ist. Wenn Sie jedoch "detable" für beide "Classe" aufzurufen, wird jeder Typ stattdessen einer eigenen Tabelle zugeordnet, auch als "TPT" bezeichnet, da jeder Typ eine eigene Tabelle hat.

``` csharp
    modelBuilder.Types()
                .Configure(c=>c.ToTable(c.ClrType.Name));
```

Der obige Code wird einer Tabellenstruktur zugeordnet, die wie folgt aussieht:

![TPT-Beispiel](~/ef6/media/tptexample.jpg)

Sie können dies vermeiden und die standardmäßige TPH-Zuordnung auf verschiedene Weise beibehalten:

1.  Ruft die Tabelle mit demselben Tabellennamen für jeden Typ in der Hierarchie auf.
2.  Wird nur für die Basisklasse der Hierarchie aufgerufen, in unserem Beispiel Mitarbeiter.

 

## <a name="execution-order"></a>Ausführungsreihenfolge

Konventionen wirken sich auf die letzte WINS-Weise aus, die mit der flüssigen API identisch ist. Dies bedeutet, dass beim Schreiben von zwei Konventionen, die dieselbe Option der gleichen Eigenschaft konfigurieren, der letzte, der ausgeführt werden muss, gewinnt. Im folgenden Code wird beispielsweise die maximale Länge aller Zeichen folgen auf 500 festgelegt, aber wir konfigurieren alle Eigenschaften namens Name im Modell so, dass Sie eine maximale Länge von 250 haben.

``` csharp
    modelBuilder.Properties<string>()
                .Configure(c => c.HasMaxLength(500));

    modelBuilder.Properties<string>()
                .Where(x => x.Name == "Name")
                .Configure(c => c.HasMaxLength(250));
```

Da die Konvention, die maximale Länge auf 250 festzulegen, hinter der Konvention liegt, die alle Zeichen folgen auf 500 festlegt, haben alle Eigenschaften namens Name in unserem Modell einen maxLength-Wert von 250, während alle anderen Zeichen folgen, wie z. b. Beschreibungen, 500 sind. Die Verwendung von Konventionen auf diese Weise bedeutet, dass Sie eine allgemeine Konvention für Typen oder Eigenschaften im Modell bereitstellen und diese dann für unterschiedliche Teilmengen überschreiben können.

Die fließende API und Daten Anmerkungen können auch verwendet werden, um eine Konvention in bestimmten Fällen zu überschreiben. Wenn wir in unserem obigen Beispiel die überflüssige API verwendet haben, um die maximale Länge einer Eigenschaft festzulegen, haben wir Sie möglicherweise vor oder nach der Konvention eingefügt, da die spezifischere, fließende API die allgemeinere Konfigurations Konvention gewinnt.

 

## <a name="built-in-conventions"></a>Integrierte Konventionen

Da benutzerdefinierte Konventionen durch die standardmäßigen Code First Konventionen beeinträchtigt werden können, kann es hilfreich sein, Konventionen hinzuzufügen, die vor oder nach einer anderen Konvention ausgeführt werden. Zu diesem Zweck können Sie die AddBefore-Methode und die AddAfter-Methode der Conventions-Auflistung in Ihrem abgeleiteten dbcontext verwenden. Der folgende Code fügt die zuvor erstellte Konventionen-Klasse hinzu, sodass Sie vor der integrierten Schlüssel Ermittlungs Konvention ausgeführt wird.

``` csharp
    modelBuilder.Conventions.AddBefore<IdKeyDiscoveryConvention>(new DateTime2Convention());
```

Dies wird beim Hinzufügen von Konventionen, die vor oder nach den integrierten Konventionen ausgeführt werden müssen, am meisten verwendet. eine Liste der integrierten Konventionen finden Sie hier: [System. Data. Entity. modelconfiguration. Conventions-Namespace](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).

Sie können auch Konventionen entfernen, die nicht auf das Modell angewendet werden sollen. Verwenden Sie zum Entfernen einer Konvention die Remove-Methode. Im folgenden finden Sie ein Beispiel für das Entfernen von pluralizingtablenameconvention.

``` csharp
    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Conventions.Remove<PluralizingTableNameConvention>();
    }
```
