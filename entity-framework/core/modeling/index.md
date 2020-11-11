---
title: Erstellen und Konfigurieren eines Modells – EF Core
description: Übersicht über das Erstellen und Konfigurieren eines Modells mit Entity Framework Core
author: AndriySvyryd
ms.date: 10/13/2020
uid: core/modeling/index
ms.openlocfilehash: b18db0059efd335abe2fc44bbc78e0106717e058
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429610"
---
# <a name="creating-and-configuring-a-model"></a><span data-ttu-id="3c268-103">Erstellen und Konfigurieren eines Modells</span><span class="sxs-lookup"><span data-stu-id="3c268-103">Creating and configuring a model</span></span>

<span data-ttu-id="3c268-104">Entity Framework verwendet eine Reihe von Konventionen, um ein Modell basierend auf der Anordnung Ihrer Entitätsklassen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="3c268-104">Entity Framework uses a set of conventions to build a model based on the shape of your entity classes.</span></span> <span data-ttu-id="3c268-105">Sie können zusätzliche Konfigurationen angeben, um die gemäß den Konventionen gewonnenen Ergebnisse zu ergänzen und/oder zu überschreiben.</span><span class="sxs-lookup"><span data-stu-id="3c268-105">You can specify additional configuration to supplement and/or override what was discovered by convention.</span></span>

<span data-ttu-id="3c268-106">Dieser Artikel behandelt die Konfiguration, die auf ein Modell für einen beliebigen Datenspeicher und auf jede relationale Datenbank angewendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="3c268-106">This article covers configuration that can be applied to a model targeting any data store and that which can be applied when targeting any relational database.</span></span> <span data-ttu-id="3c268-107">Anbieter können auch eine Konfiguration aktivieren, die speziell für einen bestimmten Datenspeicher gilt.</span><span class="sxs-lookup"><span data-stu-id="3c268-107">Providers may also enable configuration that is specific to a particular data store.</span></span> <span data-ttu-id="3c268-108">Dokumentation zu anbieterspezifischen Konfigurationen finden Sie im Abschnitt [Datenbankanbieter](xref:core/providers/index).</span><span class="sxs-lookup"><span data-stu-id="3c268-108">For documentation on provider specific configuration see the [Database Providers](xref:core/providers/index) section.</span></span>

> [!TIP]  
> <span data-ttu-id="3c268-109">Das in diesem Artikel verwendete [Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples) finden Sie auf GitHub.</span><span class="sxs-lookup"><span data-stu-id="3c268-109">You can view this article’s [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples) on GitHub.</span></span>

## <a name="use-fluent-api-to-configure-a-model"></a><span data-ttu-id="3c268-110">Verwenden der Fluent-API zum Konfigurieren eines Modells</span><span class="sxs-lookup"><span data-stu-id="3c268-110">Use fluent API to configure a model</span></span>

<span data-ttu-id="3c268-111">Sie können die `OnModelCreating`-Methode in Ihrem abgeleiteten Kontext überschreiben und mit `ModelBuilder API` Ihr Modell konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="3c268-111">You can override the `OnModelCreating` method in your derived context and use the `ModelBuilder API` to configure your model.</span></span> <span data-ttu-id="3c268-112">Dies ist die wirksamste Konfigurationsmethode und erlaubt die Angabe der Konfiguration, ohne die Entitätsklassen zu verändern.</span><span class="sxs-lookup"><span data-stu-id="3c268-112">This is the most powerful method of configuration and allows configuration to be specified without modifying your entity classes.</span></span> <span data-ttu-id="3c268-113">Die Fluent-API-Konfiguration hat die höchste Priorität und überschreibt Konventionen und Datenanmerkungen.</span><span class="sxs-lookup"><span data-stu-id="3c268-113">Fluent API configuration has the highest precedence and will override conventions and data annotations.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?highlight=12-14)]

### <a name="grouping-configuration"></a><span data-ttu-id="3c268-114">Gruppieren der Konfiguration</span><span class="sxs-lookup"><span data-stu-id="3c268-114">Grouping configuration</span></span>

<span data-ttu-id="3c268-115">Um die Größe der <xref:System.Data.Entity.DbContext.OnModelCreating%2A>-Methode zu reduzieren, kann die gesamte Konfiguration für einen Entitätstyp in eine separate Klasse extrahiert werden, indem <xref:Microsoft.EntityFrameworkCore.IEntityTypeConfiguration%601> implementiert wird.</span><span class="sxs-lookup"><span data-stu-id="3c268-115">To reduce the size of the <xref:System.Data.Entity.DbContext.OnModelCreating%2A> method all configuration for an entity type can be extracted to a separate class implementing <xref:Microsoft.EntityFrameworkCore.IEntityTypeConfiguration%601>.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/EntityTypeConfiguration.cs?Name=IEntityTypeConfiguration)]

<span data-ttu-id="3c268-116">Rufen Sie dann einfach die `Configure`-Methode aus `OnModelCreating` auf.</span><span class="sxs-lookup"><span data-stu-id="3c268-116">Then just invoke the `Configure` method from `OnModelCreating`.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/EntityTypeConfiguration.cs?Name=ApplyIEntityTypeConfiguration)]

<span data-ttu-id="3c268-117">Es ist möglich, sämtliche Konfigurationen anzuwenden, die in Typen angegeben werden, indem `IEntityTypeConfiguration` in einer bestimmten Assembly implementiert wird.</span><span class="sxs-lookup"><span data-stu-id="3c268-117">It is possible to apply all configuration specified in types implementing `IEntityTypeConfiguration` in a given assembly.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/EntityTypeConfiguration.cs?Name=ApplyConfigurationsFromAssembly)]

> [!NOTE]
> <span data-ttu-id="3c268-118">Die Reihenfolge, in der die Konfigurationen angewendet werden, ist nicht definiert. Diese Methode sollte daher nur verwendet werden, wenn die Reihenfolge keine Rolle spielt.</span><span class="sxs-lookup"><span data-stu-id="3c268-118">The order in which the configurations will be applied is undefined, therefore this method should only be used when the order doesn't matter.</span></span>

## <a name="use-data-annotations-to-configure-a-model"></a><span data-ttu-id="3c268-119">Verwenden von Datenanmerkungen zum Konfigurieren eines Modells</span><span class="sxs-lookup"><span data-stu-id="3c268-119">Use data annotations to configure a model</span></span>

<span data-ttu-id="3c268-120">Sie können auch Attribute (sogenannte „Datenanmerkungen“) auf Ihre Klassen und Eigenschaften anwenden.</span><span class="sxs-lookup"><span data-stu-id="3c268-120">You can also apply attributes (known as Data Annotations) to your classes and properties.</span></span> <span data-ttu-id="3c268-121">Datenanmerkungen setzen Konventionen außer Kraft, werden aber ihrerseits von der Fluent-API-Konfiguration außer Kraft gesetzt.</span><span class="sxs-lookup"><span data-stu-id="3c268-121">Data annotations will override conventions, but will be overridden by Fluent API configuration.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?highlight=15)]
