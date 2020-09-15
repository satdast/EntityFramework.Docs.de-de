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
# <a name="model-based-conventions"></a><span data-ttu-id="20176-103">Modellbasierte Konventionen</span><span class="sxs-lookup"><span data-stu-id="20176-103">Model-Based Conventions</span></span>
> [!NOTE]
> <span data-ttu-id="20176-104">**Nur EF6 und höher:** Die Features, APIs usw., die auf dieser Seite erläutert werden, wurden in Entity Framework 6 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="20176-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="20176-105">Wenn Sie eine frühere Version verwenden, gelten manche Informationen nicht.</span><span class="sxs-lookup"><span data-stu-id="20176-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="20176-106">Modellbasierte Konventionen sind eine erweiterte Methode der Modell Konfiguration, die auf der Konvention basiert.</span><span class="sxs-lookup"><span data-stu-id="20176-106">Model based conventions are an advanced method of convention based model configuration.</span></span> <span data-ttu-id="20176-107">In den meisten Szenarien sollte die API für die [benutzerdefinierte Code First Konvention in dbmodelbuilder](xref:ef6/modeling/code-first/conventions/custom) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="20176-107">For most scenarios the [Custom Code First Convention API on DbModelBuilder](xref:ef6/modeling/code-first/conventions/custom) should be used.</span></span> <span data-ttu-id="20176-108">Vor der Verwendung von modellbasierten Konventionen wird ein Verständnis der dbmodelbuilder-API für Konventionen empfohlen.</span><span class="sxs-lookup"><span data-stu-id="20176-108">An understanding of the DbModelBuilder API for conventions is recommended before using model based conventions.</span></span>  

<span data-ttu-id="20176-109">Modellbasierte Konventionen ermöglichen die Erstellung von Konventionen, die sich auf Eigenschaften und Tabellen auswirken, die nicht über Standard Konventionen konfiguriert werden können.</span><span class="sxs-lookup"><span data-stu-id="20176-109">Model based conventions allow the creation of conventions that affect properties and tables which are not configurable through standard conventions.</span></span> <span data-ttu-id="20176-110">Beispiele hierfür sind diskriminatorspalten in Tabelle pro Hierarchie Modellen und unabhängigen Assoziations Spalten.</span><span class="sxs-lookup"><span data-stu-id="20176-110">Examples of these are discriminator columns in table per hierarchy models and Independent Association columns.</span></span>  

## <a name="creating-a-convention"></a><span data-ttu-id="20176-111">Erstellen einer Konvention</span><span class="sxs-lookup"><span data-stu-id="20176-111">Creating a Convention</span></span>   

<span data-ttu-id="20176-112">Der erste Schritt beim Erstellen einer modellbasierten Konvention ist die Entscheidung, wann in der Pipeline die Konvention auf das Modell angewendet werden muss.</span><span class="sxs-lookup"><span data-stu-id="20176-112">The first step in creating a model based convention is choosing when in the pipeline the convention needs to be applied to the model.</span></span> <span data-ttu-id="20176-113">Es gibt zwei Arten von Modell Konventionen: konzeptionell (C-Space) und Speicher (S-Space).</span><span class="sxs-lookup"><span data-stu-id="20176-113">There are two types of model conventions, Conceptual (C-Space) and Store (S-Space).</span></span> <span data-ttu-id="20176-114">Eine C-Space-Konvention wird auf das Modell angewendet, das von der Anwendung erstellt wird, wohingegen eine S-Space-Konvention auf die Version des Modells angewendet wird, die die Datenbank darstellt, und steuert, wie automatisch generierte Spalten benannt werden.</span><span class="sxs-lookup"><span data-stu-id="20176-114">A C-Space convention is applied to the model that the application builds, whereas an S-Space convention is applied to the version of the model that represents the database and controls things such as how automatically-generated columns are named.</span></span>  

<span data-ttu-id="20176-115">Eine Modell Konvention ist eine Klasse, die sich entweder von ikonzeptionalmodelconvention oder istoremodelconvention erstreckt.</span><span class="sxs-lookup"><span data-stu-id="20176-115">A model convention is a class that extends from either IConceptualModelConvention or IStoreModelConvention.</span></span>  <span data-ttu-id="20176-116">Diese Schnittstellen akzeptieren beide einen generischen Typ, der vom Typ "MetadataItem" sein kann, der zum Filtern des Datentyps verwendet wird, für den die Konvention gilt.</span><span class="sxs-lookup"><span data-stu-id="20176-116">These interfaces both accept a generic type that can be of type MetadataItem which is used to filter the data type that the convention applies to.</span></span>  

## <a name="adding-a-convention"></a><span data-ttu-id="20176-117">Hinzufügen einer Konvention</span><span class="sxs-lookup"><span data-stu-id="20176-117">Adding a Convention</span></span>   

<span data-ttu-id="20176-118">Modell Konventionen werden auf die gleiche Weise wie reguläre Konventionen-Klassen hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="20176-118">Model conventions are added in the same way as regular conventions classes.</span></span> <span data-ttu-id="20176-119">Fügen Sie in der **onmodelcreating** -Methode der Liste der Konventionen für ein Modell die Konvention hinzu.</span><span class="sxs-lookup"><span data-stu-id="20176-119">In the **OnModelCreating** method, add the convention to the list of conventions for a model.</span></span>  

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

<span data-ttu-id="20176-120">Eine Konvention kann auch mit der Konventionen. AddBefore- \<\> Methode oder der Conventions. AddAfter-Methode in Relation zu einer anderen Konvention hinzugefügt werden \<\> .</span><span class="sxs-lookup"><span data-stu-id="20176-120">A convention can also be added in relation to another convention using the Conventions.AddBefore\<\> or Conventions.AddAfter\<\> methods.</span></span> <span data-ttu-id="20176-121">Weitere Informationen zu den Konventionen, die Entity Framework angewendet wird, finden Sie im Abschnitt "Hinweise".</span><span class="sxs-lookup"><span data-stu-id="20176-121">For more information about the conventions that Entity Framework applies see the notes section.</span></span>  

``` csharp
protected override void OnModelCreating(DbModelBuilder modelBuilder)
{
    modelBuilder.Conventions.AddAfter<IdKeyDiscoveryConvention>(new MyModelBasedConvention());
}
```  

## <a name="example-discriminator-model-convention"></a><span data-ttu-id="20176-122">Beispiel: diskriminatormodellkonvention</span><span class="sxs-lookup"><span data-stu-id="20176-122">Example: Discriminator Model Convention</span></span>  

<span data-ttu-id="20176-123">Das Umbenennen von von EF generierten Spalten ist ein Beispiel für etwas, das Sie mit den anderen Konventionen-APIs nicht ausführen können.</span><span class="sxs-lookup"><span data-stu-id="20176-123">Renaming columns generated by EF is an example of something that you can’t do with the other conventions APIs.</span></span>  <span data-ttu-id="20176-124">Dies ist eine Situation, in der die Verwendung von Modell Konventionen Ihre einzige Option ist.</span><span class="sxs-lookup"><span data-stu-id="20176-124">This is a situation where using model conventions is your only option.</span></span>  

<span data-ttu-id="20176-125">Ein Beispiel für die Verwendung einer modellbasierten Konvention zum Konfigurieren generierter Spalten ist die Anpassung der Art und Weise, wie diskriminatorspalten benannt werden.</span><span class="sxs-lookup"><span data-stu-id="20176-125">An example of how to use a model based convention to configure generated columns is customizing the way discriminator columns are named.</span></span>  <span data-ttu-id="20176-126">Im folgenden finden Sie ein Beispiel für eine einfache modellbasierte Konvention, die jede Spalte im Modell mit dem Namen "Diskriminator" in "EntityType" umbenennt.</span><span class="sxs-lookup"><span data-stu-id="20176-126">Below is an example of a simple model based convention that renames every column in the model named “Discriminator” to “EntityType”.</span></span>  <span data-ttu-id="20176-127">Dies schließt Spalten ein, die der Entwickler einfach als "Diskriminator" bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="20176-127">This includes columns that the developer simply named “Discriminator”.</span></span> <span data-ttu-id="20176-128">Da es sich bei der Spalte "Diskriminator" um eine generierte Spalte handelt, muss diese im Bereich von S ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="20176-128">Since the “Discriminator” column is a generated column this needs to run in S-Space.</span></span>  

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

## <a name="example-general-ia-renaming-convention"></a><span data-ttu-id="20176-129">Beispiel: allgemeine IA-Umbenennungs Konvention</span><span class="sxs-lookup"><span data-stu-id="20176-129">Example: General IA Renaming Convention</span></span>   

<span data-ttu-id="20176-130">Ein weiteres komplizierteres Beispiel für modellbasierte Konventionen in Aktion besteht darin, die Art und Weise zu konfigurieren, in der unabhängige Zuordnungen (IAS) benannt werden.</span><span class="sxs-lookup"><span data-stu-id="20176-130">Another more complicated example of model based conventions in action is to configure the way that Independent Associations (IAs) are named.</span></span>  <span data-ttu-id="20176-131">Dies ist eine Situation, in der Modell Konventionen zutreffen, da IAS von EF generiert wird und nicht im Modell vorhanden ist, auf das die dbmodelbuilder-API zugreifen kann.</span><span class="sxs-lookup"><span data-stu-id="20176-131">This is a situation where Model conventions are applicable because IAs are generated by EF and aren’t present in the model that the DbModelBuilder API can access.</span></span>  

<span data-ttu-id="20176-132">Wenn EF eine IA generiert, erstellt es eine Spalte mit dem Namen EntityType_KeyName.</span><span class="sxs-lookup"><span data-stu-id="20176-132">When EF generates an IA, it creates a column named EntityType_KeyName.</span></span> <span data-ttu-id="20176-133">Beispielsweise würde für eine Zuordnung mit dem Namen Customer und einer Schlüssel Spalte mit dem Namen CustomerID eine Spalte mit dem Namen Customer_CustomerId generiert werden.</span><span class="sxs-lookup"><span data-stu-id="20176-133">For example, for an association named Customer with a key column named CustomerId it would generate a column named Customer_CustomerId.</span></span> <span data-ttu-id="20176-134">In der folgenden Konvention wird das \_ Zeichen "" aus dem für die IA generierten Spaltennamen entfernt.</span><span class="sxs-lookup"><span data-stu-id="20176-134">The following convention strips the ‘\_’ character out of the column name that is generated for the IA.</span></span>  

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

## <a name="extending-existing-conventions"></a><span data-ttu-id="20176-135">Erweitern vorhandener Konventionen</span><span class="sxs-lookup"><span data-stu-id="20176-135">Extending Existing Conventions</span></span>   

<span data-ttu-id="20176-136">Wenn Sie eine Konvention schreiben müssen, die einer der Konventionen ähnelt, die Entity Framework bereits auf Ihr Modell angewendet haben, können Sie diese Konvention immer erweitern, um zu vermeiden, dass Sie von Grund auf neu geschrieben werden muss.</span><span class="sxs-lookup"><span data-stu-id="20176-136">If you need to write a convention that is similar to one of the conventions that Entity Framework already applies to your model you can always extend that convention to avoid having to rewrite it from scratch.</span></span>  <span data-ttu-id="20176-137">Ein Beispiel hierfür besteht darin, die vorhandene ID-abgleichskonvention durch eine benutzerdefinierte zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="20176-137">An example of this is to replace the existing Id matching convention with a custom one.</span></span>   <span data-ttu-id="20176-138">Ein zusätzlicher Vorteil beim Überschreiben der Schlüssel Konvention besteht darin, dass die überschriebene Methode nur aufgerufen wird, wenn kein Schlüssel bereits erkannt oder explizit konfiguriert wurde.</span><span class="sxs-lookup"><span data-stu-id="20176-138">An added benefit to overriding the key convention is that the overridden method will get called only if there is no key already detected or explicitly configured.</span></span> <span data-ttu-id="20176-139">Eine Liste der Konventionen, die von Entity Framework verwendet werden, finden Sie hier: [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx) .</span><span class="sxs-lookup"><span data-stu-id="20176-139">A list of conventions that used by Entity Framework is available here: [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span></span>  

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

<span data-ttu-id="20176-140">Anschließend müssen wir unsere neue Konvention vor der vorhandenen Schlüssel Konvention hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="20176-140">We then need to add our new convention before the existing key convention.</span></span> <span data-ttu-id="20176-141">Nachdem wir customkeydiscoveryconvention hinzugefügt haben, können wir idkeydiscoveryconvention entfernen.</span><span class="sxs-lookup"><span data-stu-id="20176-141">After we add the CustomKeyDiscoveryConvention, we can remove the IdKeyDiscoveryConvention.</span></span>  <span data-ttu-id="20176-142">Wenn wir die vorhandene idkeydiscoveryconvention nicht entfernt haben, hat diese Konvention weiterhin Vorrang vor der ID-Ermittlungs Konvention, da Sie zuerst ausgeführt wird. Wenn jedoch keine "Key"-Eigenschaft gefunden wird, wird die "ID"-Konvention ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="20176-142">If we didn’t remove the existing IdKeyDiscoveryConvention this convention would still take precedence over the Id discovery convention since it is run first, but in the case where no “key” property is found, the “id” convention will run.</span></span>  <span data-ttu-id="20176-143">Dieses Verhalten wird angezeigt, weil bei jeder Konvention das Modell wie in der vorherigen Konvention aktualisiert angezeigt wird (anstatt sie unabhängig voneinander zu betreiben), sodass eine vorherige Konvention z. b. einen Spaltennamen so aktualisiert hat, dass Sie etwas, das für Ihre benutzerdefinierte Konvention relevant ist (wenn der Name nicht von Interesse war), auf diese Spalte angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="20176-143">We see this behavior because each convention sees the model as updated by the previous convention (rather than operating on it independently and all being combined together) so that if for example, a previous convention updated a column name to match something of interest to your custom convention (when before that the name was not of interest) then it will apply to that column.</span></span>  

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

## <a name="notes"></a><span data-ttu-id="20176-144">Notizen</span><span class="sxs-lookup"><span data-stu-id="20176-144">Notes</span></span>  

<span data-ttu-id="20176-145">Eine Liste der Konventionen, die zurzeit von Entity Framework angewendet werden, finden Sie in der MSDN-Dokumentation unter: [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx) .</span><span class="sxs-lookup"><span data-stu-id="20176-145">A list of conventions that are currently applied by Entity Framework is available in the MSDN documentation here: [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span></span>  <span data-ttu-id="20176-146">Diese Liste wird direkt aus dem Quellcode abgerufen.</span><span class="sxs-lookup"><span data-stu-id="20176-146">This list is pulled directly from our source code.</span></span>  <span data-ttu-id="20176-147">Der Quellcode für Entity Framework 6 ist auf [GitHub](https://github.com/aspnet/entityframework6/) verfügbar, und viele der von Entity Framework verwendeten Konventionen sind gute Ausgangspunkte für benutzerdefinierte modellbasierte Konventionen.</span><span class="sxs-lookup"><span data-stu-id="20176-147">The source code for Entity Framework 6 is available on [GitHub](https://github.com/aspnet/entityframework6/) and many of the conventions used by Entity Framework are good starting points for custom model based conventions.</span></span>  
