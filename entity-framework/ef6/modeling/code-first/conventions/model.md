---
title: Modellbasierte Konventionen EF6
description: Modellbasierte Konventionen in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/conventions/model
ms.openlocfilehash: bcb7a88f4d9fc994b51300ac30ec58671be370c8
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90074002"
---
# <a name="model-based-conventions"></a>Modellbasierte Konventionen
> [!NOTE]
> **Nur EF6 und höher:** Die Features, APIs usw., die auf dieser Seite erläutert werden, wurden in Entity Framework 6 eingeführt. Wenn Sie eine frühere Version verwenden, gelten manche Informationen nicht.  

Modellbasierte Konventionen sind eine erweiterte Methode der Modell Konfiguration, die auf der Konvention basiert. In den meisten Szenarien sollte die API für die [benutzerdefinierte Code First Konvention in dbmodelbuilder](xref:ef6/modeling/code-first/conventions/custom) verwendet werden. Vor der Verwendung von modellbasierten Konventionen wird ein Verständnis der dbmodelbuilder-API für Konventionen empfohlen.  

Modellbasierte Konventionen ermöglichen die Erstellung von Konventionen, die sich auf Eigenschaften und Tabellen auswirken, die nicht über Standard Konventionen konfiguriert werden können. Beispiele hierfür sind diskriminatorspalten in Tabelle pro Hierarchie Modellen und unabhängigen Assoziations Spalten.  

## <a name="creating-a-convention"></a>Erstellen einer Konvention   

Der erste Schritt beim Erstellen einer modellbasierten Konvention ist die Entscheidung, wann in der Pipeline die Konvention auf das Modell angewendet werden muss. Es gibt zwei Arten von Modell Konventionen: konzeptionell (C-Space) und Speicher (S-Space). Eine C-Space-Konvention wird auf das Modell angewendet, das von der Anwendung erstellt wird, wohingegen eine S-Space-Konvention auf die Version des Modells angewendet wird, die die Datenbank darstellt, und steuert, wie automatisch generierte Spalten benannt werden.  

Eine Modell Konvention ist eine Klasse, die sich entweder von ikonzeptionalmodelconvention oder istoremodelconvention erstreckt.  Diese Schnittstellen akzeptieren beide einen generischen Typ, der vom Typ "MetadataItem" sein kann, der zum Filtern des Datentyps verwendet wird, für den die Konvention gilt.  

## <a name="adding-a-convention"></a>Hinzufügen einer Konvention   

Modell Konventionen werden auf die gleiche Weise wie reguläre Konventionen-Klassen hinzugefügt. Fügen Sie in der **onmodelcreating** -Methode der Liste der Konventionen für ein Modell die Konvention hinzu.  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Core.Metadata.Edm;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.ModelConfiguration.Conventions;

public class BlogContext : DbContext  
{  
    public DbSet<Post> Posts { get; set; }  
    public DbSet<Comment> Comments { get; set; }  

    protected override void OnModelCreating(DbModelBuilder modelBuilder)  
    {  
        modelBuilder.Conventions.Add<MyModelBasedConvention>();  
    }  
}
```  

Eine Konvention kann auch mit der Konventionen. AddBefore- \<\> Methode oder der Conventions. AddAfter-Methode in Relation zu einer anderen Konvention hinzugefügt werden \<\> . Weitere Informationen zu den Konventionen, die Entity Framework angewendet wird, finden Sie im Abschnitt "Hinweise".  

``` csharp
protected override void OnModelCreating(DbModelBuilder modelBuilder)
{
    modelBuilder.Conventions.AddAfter<IdKeyDiscoveryConvention>(new MyModelBasedConvention());
}
```  

## <a name="example-discriminator-model-convention"></a>Beispiel: diskriminatormodellkonvention  

Das Umbenennen von von EF generierten Spalten ist ein Beispiel für etwas, das Sie mit den anderen Konventionen-APIs nicht ausführen können.  Dies ist eine Situation, in der die Verwendung von Modell Konventionen Ihre einzige Option ist.  

Ein Beispiel für die Verwendung einer modellbasierten Konvention zum Konfigurieren generierter Spalten ist die Anpassung der Art und Weise, wie diskriminatorspalten benannt werden.  Im folgenden finden Sie ein Beispiel für eine einfache modellbasierte Konvention, die jede Spalte im Modell mit dem Namen "Diskriminator" in "EntityType" umbenennt.  Dies schließt Spalten ein, die der Entwickler einfach als "Diskriminator" bezeichnet. Da es sich bei der Spalte "Diskriminator" um eine generierte Spalte handelt, muss diese im Bereich von S ausgeführt werden.  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Core.Metadata.Edm;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.ModelConfiguration.Conventions;

class DiscriminatorRenamingConvention : IStoreModelConvention<EdmProperty>  
{  
    public void Apply(EdmProperty property, DbModel model)  
    {            
        if (property.Name == "Discriminator")  
        {  
            property.Name = "EntityType";  
        }  
    }  
}
```  

## <a name="example-general-ia-renaming-convention"></a>Beispiel: allgemeine IA-Umbenennungs Konvention   

Ein weiteres komplizierteres Beispiel für modellbasierte Konventionen in Aktion besteht darin, die Art und Weise zu konfigurieren, in der unabhängige Zuordnungen (IAS) benannt werden.  Dies ist eine Situation, in der Modell Konventionen zutreffen, da IAS von EF generiert wird und nicht im Modell vorhanden ist, auf das die dbmodelbuilder-API zugreifen kann.  

Wenn EF eine IA generiert, erstellt es eine Spalte mit dem Namen EntityType_KeyName. Beispielsweise würde für eine Zuordnung mit dem Namen Customer und einer Schlüssel Spalte mit dem Namen CustomerID eine Spalte mit dem Namen Customer_CustomerId generiert werden. In der folgenden Konvention wird das \_ Zeichen "" aus dem für die IA generierten Spaltennamen entfernt.  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Core.Metadata.Edm;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.ModelConfiguration.Conventions;

// Provides a convention for fixing the independent association (IA) foreign key column names.  
public class ForeignKeyNamingConvention : IStoreModelConvention<AssociationType>
{

    public void Apply(AssociationType association, DbModel model)
    {
        // Identify ForeignKey properties (including IAs)  
        if (association.IsForeignKey)
        {
            // rename FK columns  
            var constraint = association.Constraint;
            if (DoPropertiesHaveDefaultNames(constraint.FromProperties, constraint.ToRole.Name, constraint.ToProperties))
            {
                NormalizeForeignKeyProperties(constraint.FromProperties);
            }
            if (DoPropertiesHaveDefaultNames(constraint.ToProperties, constraint.FromRole.Name, constraint.FromProperties))
            {
                NormalizeForeignKeyProperties(constraint.ToProperties);
            }
        }
    }

    private bool DoPropertiesHaveDefaultNames(ReadOnlyMetadataCollection<EdmProperty> properties, string roleName, ReadOnlyMetadataCollection<EdmProperty> otherEndProperties)
    {
        if (properties.Count != otherEndProperties.Count)
        {
            return false;
        }

        for (int i = 0; i < properties.Count; ++i)
        {
            if (!properties[i].Name.EndsWith("_" + otherEndProperties[i].Name))
            {
                return false;
            }
        }
        return true;
    }

    private void NormalizeForeignKeyProperties(ReadOnlyMetadataCollection<EdmProperty> properties)
    {
        for (int i = 0; i < properties.Count; ++i)
        {
            int underscoreIndex = properties[i].Name.IndexOf('_');
            if (underscoreIndex > 0)
            {
                properties[i].Name = properties[i].Name.Remove(underscoreIndex, 1);
            }                 
        }
    }
}
```  

## <a name="extending-existing-conventions"></a>Erweitern vorhandener Konventionen   

Wenn Sie eine Konvention schreiben müssen, die einer der Konventionen ähnelt, die Entity Framework bereits auf Ihr Modell angewendet haben, können Sie diese Konvention immer erweitern, um zu vermeiden, dass Sie von Grund auf neu geschrieben werden muss.  Ein Beispiel hierfür besteht darin, die vorhandene ID-abgleichskonvention durch eine benutzerdefinierte zu ersetzen.   Ein zusätzlicher Vorteil beim Überschreiben der Schlüssel Konvention besteht darin, dass die überschriebene Methode nur aufgerufen wird, wenn kein Schlüssel bereits erkannt oder explizit konfiguriert wurde. Eine Liste der Konventionen, die von Entity Framework verwendet werden, finden Sie hier: [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx) .  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Core.Metadata.Edm;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.ModelConfiguration.Conventions;
using System.Linq;  

// Convention to detect primary key properties.
// Recognized naming patterns in order of precedence are:
// 1. 'Key'
// 2. [type name]Key
// Primary key detection is case insensitive.
public class CustomKeyDiscoveryConvention : KeyDiscoveryConvention
{
    private const string Id = "Key";

    protected override IEnumerable<EdmProperty> MatchKeyProperty(
        EntityType entityType, IEnumerable<EdmProperty> primitiveProperties)
    {
        Debug.Assert(entityType != null);
        Debug.Assert(primitiveProperties != null);

        var matches = primitiveProperties
            .Where(p => Id.Equals(p.Name, StringComparison.OrdinalIgnoreCase));

        if (!matches.Any())
       {
            matches = primitiveProperties
                .Where(p => (entityType.Name + Id).Equals(p.Name, StringComparison.OrdinalIgnoreCase));
        }

        // If the number of matches is more than one, then multiple properties matched differing only by
        // case--for example, "Key" and "key".  
        if (matches.Count() > 1)
        {
            throw new InvalidOperationException("Multiple properties match the key convention");
        }

        return matches;
    }
}
```  

Anschließend müssen wir unsere neue Konvention vor der vorhandenen Schlüssel Konvention hinzufügen. Nachdem wir customkeydiscoveryconvention hinzugefügt haben, können wir idkeydiscoveryconvention entfernen.  Wenn wir die vorhandene idkeydiscoveryconvention nicht entfernt haben, hat diese Konvention weiterhin Vorrang vor der ID-Ermittlungs Konvention, da Sie zuerst ausgeführt wird. Wenn jedoch keine "Key"-Eigenschaft gefunden wird, wird die "ID"-Konvention ausgeführt.  Dieses Verhalten wird angezeigt, weil bei jeder Konvention das Modell wie in der vorherigen Konvention aktualisiert angezeigt wird (anstatt sie unabhängig voneinander zu betreiben), sodass eine vorherige Konvention z. b. einen Spaltennamen so aktualisiert hat, dass Sie etwas, das für Ihre benutzerdefinierte Konvention relevant ist (wenn der Name nicht von Interesse war), auf diese Spalte angewendet wird.  

``` csharp
public class BlogContext : DbContext
{
    public DbSet<Post> Posts { get; set; }
    public DbSet<Comment> Comments { get; set; }

    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Conventions.AddBefore<IdKeyDiscoveryConvention>(new CustomKeyDiscoveryConvention());
        modelBuilder.Conventions.Remove<IdKeyDiscoveryConvention>();
    }
}
```  

## <a name="notes"></a>Notizen  

Eine Liste der Konventionen, die zurzeit von Entity Framework angewendet werden, finden Sie in der MSDN-Dokumentation unter: [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx) .  Diese Liste wird direkt aus dem Quellcode abgerufen.  Der Quellcode für Entity Framework 6 ist auf [GitHub](https://github.com/aspnet/entityframework6/) verfügbar, und viele der von Entity Framework verwendeten Konventionen sind gute Ausgangspunkte für benutzerdefinierte modellbasierte Konventionen.  
