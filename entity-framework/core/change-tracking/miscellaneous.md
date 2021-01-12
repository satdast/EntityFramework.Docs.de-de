---
title: Zusätzliche Änderungsnachverfolgung Features-EF Core
description: Verschiedene Features und Szenarien im Zusammenhang mit EF Core Änderungs Nachverfolgung
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/miscellaneous
ms.openlocfilehash: db1e32948b2a60ad1b85e300bbbccd54d49a84e5
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129595"
---
# <a name="additional-change-tracking-features"></a><span data-ttu-id="d8d2d-103">Zusätzliche Änderungsnachverfolgung Features</span><span class="sxs-lookup"><span data-stu-id="d8d2d-103">Additional Change Tracking Features</span></span>

<span data-ttu-id="d8d2d-104">Dieses Dokument behandelt verschiedene Features und Szenarien im Zusammenhang mit der Änderungs Nachverfolgung.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-104">This document covers miscellaneous features and scenarios involving change tracking.</span></span>

> [!TIP]
> <span data-ttu-id="d8d2d-105">In diesem Dokument wird davon ausgegangen, dass die Entitäts Zustände und die Grundlagen EF Core Änderungs Nachverfolgung verstanden werden.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-105">This document assumes that entity states and the basics of EF Core change tracking are understood.</span></span> <span data-ttu-id="d8d2d-106">Weitere Informationen zu diesen Themen finden Sie [unter Änderungsnachverfolgung in EF Core](xref:core/change-tracking/index) .</span><span class="sxs-lookup"><span data-stu-id="d8d2d-106">See [Change Tracking in EF Core](xref:core/change-tracking/index) for more information on these topics.</span></span>

> [!TIP]
> <span data-ttu-id="d8d2d-107">Sie können den gesamten Code in diesem Dokument ausführen und Debuggen, indem Sie [den Beispielcode von GitHub herunterladen](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/AdditionalChangeTrackingFeatures).</span><span class="sxs-lookup"><span data-stu-id="d8d2d-107">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/AdditionalChangeTrackingFeatures).</span></span>

## <a name="add-verses-addasync"></a><span data-ttu-id="d8d2d-108">Verse hinzufügen AddAsync</span><span class="sxs-lookup"><span data-stu-id="d8d2d-108">Add verses AddAsync</span></span>

<span data-ttu-id="d8d2d-109">Entity Framework Core (EF Core) stellt asynchrone Methoden bereit, wenn die Verwendung dieser Methode eine Daten Bank Interaktion verursachen kann.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-109">Entity Framework Core (EF Core) provides async methods whenever using that method may result in a database interaction.</span></span> <span data-ttu-id="d8d2d-110">Synchrone Methoden werden ebenfalls bereitgestellt, um den mehr Aufwand bei der Verwendung von Datenbanken zu vermeiden, die keinen asynchronen Hochleistungs Zugriff unterstützen</span><span class="sxs-lookup"><span data-stu-id="d8d2d-110">Synchronous methods are also provided to avoid overhead when using databases that do not support high performance asynchronous access.</span></span>

<span data-ttu-id="d8d2d-111"><xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> und <xref:Microsoft.EntityFrameworkCore.DbSet%601.Add%2A?displayProperty=nameWithType> greifen in der Regel nicht auf die Datenbank zu, da diese Methoden grundsätzlich nur die Nachverfolgung von Entitäten starten.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-111"><xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.DbSet%601.Add%2A?displayProperty=nameWithType> do not normally access the database, since these methods inherently just start tracking entities.</span></span> <span data-ttu-id="d8d2d-112">Einige Formen der Wert Generierung _können_ jedoch auf die Datenbank zugreifen, um einen Schlüsselwert zu generieren.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-112">However, some forms of value generation _may_ access the database in order to generate a key value.</span></span> <span data-ttu-id="d8d2d-113">Der einzige Wert Generator, der diese übernimmt und mit EF Core ausgeliefert wird, ist <xref:Microsoft.EntityFrameworkCore.ValueGeneration.HiLoValueGenerator%601> .</span><span class="sxs-lookup"><span data-stu-id="d8d2d-113">The only value generator that does this and ships with EF Core is <xref:Microsoft.EntityFrameworkCore.ValueGeneration.HiLoValueGenerator%601>.</span></span> <span data-ttu-id="d8d2d-114">Die Verwendung dieses Generators ist nicht üblich. Er wird nie standardmäßig konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-114">Using this generator is uncommon; it is never configured by default.</span></span> <span data-ttu-id="d8d2d-115">Dies bedeutet, dass die große Mehrheit der Anwendungen verwenden sollte `Add` und nicht `AddAsync` .</span><span class="sxs-lookup"><span data-stu-id="d8d2d-115">This means that the vast majority of applications should use `Add`, and not `AddAsync`.</span></span>

<span data-ttu-id="d8d2d-116">Andere ähnliche Methoden wie `Update` , `Attach` und `Remove` verfügen nicht über über Ladungen, da Sie nie neue Schlüsselwerte generieren und daher nie auf die Datenbank zugreifen müssen.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-116">Other similar methods like `Update`, `Attach`, and `Remove` do not have async overloads because they never generate new key values, and hence never need to access the database.</span></span>

## <a name="addrange-updaterange-attachrange-and-removerange"></a><span data-ttu-id="d8d2d-117">"AddRange", "updaterange", "attachrange" und "RemoveRange"</span><span class="sxs-lookup"><span data-stu-id="d8d2d-117">AddRange, UpdateRange, AttachRange, and RemoveRange</span></span>

<span data-ttu-id="d8d2d-118"><xref:Microsoft.EntityFrameworkCore.DbSet%601> und <xref:Microsoft.EntityFrameworkCore.DbContext> stellen Alternative Versionen von `Add` , `Update` , `Attach` und bereit `Remove` , die mehrere Instanzen in einem einzelnen-Befehl akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-118"><xref:Microsoft.EntityFrameworkCore.DbSet%601> and <xref:Microsoft.EntityFrameworkCore.DbContext> provide alternate versions of `Add`, `Update`, `Attach`, and `Remove` that accept multiple instances in a single call.</span></span> <span data-ttu-id="d8d2d-119">Diese Methoden werden `AddRange` , `UpdateRange` , `AttachRange` `RemoveRange` bzw. genannt.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-119">These methods are called `AddRange`, `UpdateRange`, `AttachRange`, and `RemoveRange` respectively.</span></span>

<span data-ttu-id="d8d2d-120">Diese Methoden werden als praktische Hilfe bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-120">These methods are provided as a convenience.</span></span> <span data-ttu-id="d8d2d-121">Die Verwendung einer "Range"-Methode hat die gleiche Funktionalität wie mehrere Aufrufe der äquivalenten Non-Range-Methode.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-121">Using a "range" method has the same functionality as multiple calls to the equivalent non-range method.</span></span> <span data-ttu-id="d8d2d-122">Es gibt keinen signifikanten Leistungsunterschied zwischen den beiden Ansätzen.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-122">There is no significant performance difference between the two approaches.</span></span>

> [!NOTE]
> <span data-ttu-id="d8d2d-123">Dies unterscheidet sich von EF6, wobei "AddRange" sowohl automatisch als "DetectChanges" bezeichnet wird, der Aufruf von "Add" jedoch mehrmals bewirkt hat, dass "DetectChanges" mehrmals anstatt einmal aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-123">This is different from EF6, where AddRange and Add both automatically called DetectChanges, but calling Add multiple times caused DetectChanges to be called multiple times instead of once.</span></span> <span data-ttu-id="d8d2d-124">Dadurch wurde AddRange in EF6 effizienter.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-124">This made AddRange more efficient in EF6.</span></span> <span data-ttu-id="d8d2d-125">In EF Core ruft diese Methode nicht automatisch DetectChanges auf.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-125">In EF Core, neither of these methods automatically call DetectChanges.</span></span>

## <a name="dbcontext-verses-dbset-methods"></a><span data-ttu-id="d8d2d-126">Dbcontext Verse dbset-Methoden</span><span class="sxs-lookup"><span data-stu-id="d8d2d-126">DbContext verses DbSet methods</span></span>

<span data-ttu-id="d8d2d-127">Viele Methoden, einschließlich `Add` , `Update` , `Attach` und `Remove` , verfügen über Implementierungen sowohl für <xref:Microsoft.EntityFrameworkCore.DbSet%601> als auch für <xref:Microsoft.EntityFrameworkCore.DbContext> .</span><span class="sxs-lookup"><span data-stu-id="d8d2d-127">Many methods, including `Add`, `Update`, `Attach`, and `Remove`, have implementations on both <xref:Microsoft.EntityFrameworkCore.DbSet%601> and <xref:Microsoft.EntityFrameworkCore.DbContext>.</span></span> <span data-ttu-id="d8d2d-128">Diese Methoden weisen _genau das gleiche Verhalten_ für normale Entitäts Typen auf.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-128">These methods have _exactly the same behavior_ for normal entity types.</span></span> <span data-ttu-id="d8d2d-129">Dies liegt daran, dass der CLR-Typ der Entität nur einem Entitätstyp im EF Core Modell zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-129">This is because the CLR type of the entity is mapped onto one and only one entity type in the EF Core model.</span></span> <span data-ttu-id="d8d2d-130">Der CLR-Typ definiert daher vollständig, wo die Entität in das Modell passt, sodass das zu verwendende dbset implizit bestimmt werden kann.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-130">Therefore, the CLR type fully defines where the entity fits in the model, and so the DbSet to use can be determined implicitly.</span></span>

<span data-ttu-id="d8d2d-131">Eine Ausnahme von dieser Regel ist die Verwendung von Entitäts Typen vom Typ "freigegeben", die in EF Core 5,0 eingeführt wurden, hauptsächlich für m:n-joinentitäten.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-131">The exception to this rule is when using shared-type entity types, which were introduced in EF Core 5.0, primarily for many-to-many join entities.</span></span> <span data-ttu-id="d8d2d-132">Wenn Sie einen Entitätstyp vom Typ "Shared" verwenden, muss zunächst ein dbset für den verwendeten EF Core Modelltyp erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-132">When using a shared-type entity type, a DbSet must first be created for the EF Core model type that is being used.</span></span> <span data-ttu-id="d8d2d-133">Methoden wie `Add` , `Update` , `Attach` und `Remove` können dann für das dbset ohne Mehrdeutigkeit verwendet werden, wenn EF Core Modelltyp verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-133">Methods like `Add`, `Update`, `Attach`, and `Remove` can then be used on the DbSet without any ambiguity as to which EF Core model type is being used.</span></span>

<span data-ttu-id="d8d2d-134">Entitäts Typen mit gemeinsam genutzten Typen werden standardmäßig für die joinentitäten in m:n-Beziehungen verwendet.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-134">Shared-type entity types are used by default for the join entities in many-to-many relationships.</span></span> <span data-ttu-id="d8d2d-135">Ein Entitätstyp vom Typ "freigegeben" kann auch explizit für die Verwendung in einer m:n-Beziehung konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-135">A shared-type entity type can also be explicitly configured for use in a many-to-many relationship.</span></span> <span data-ttu-id="d8d2d-136">Der folgende Code konfiguriert z. b. `Dictionary<string, int>` als joinentitätstyp:</span><span class="sxs-lookup"><span data-stu-id="d8d2d-136">For example, the code below configures `Dictionary<string, int>` as a join entity type:</span></span>

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder
                .SharedTypeEntity<Dictionary<string, int>>(
                    "PostTag",
                    b =>
                        {
                            b.IndexerProperty<int>("TagId");
                            b.IndexerProperty<int>("PostId");
                        });

            modelBuilder.Entity<Post>()
                .HasMany(p => p.Tags)
                .WithMany(p => p.Posts)
                .UsingEntity<Dictionary<string, int>>(
                    "PostTag",
                    j => j.HasOne<Tag>().WithMany(),
                    j => j.HasOne<Post>().WithMany());
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/Samples.cs?name=OnModelCreating)]

<span data-ttu-id="d8d2d-137">[Ändern von Fremdschlüsseln und Navigationen](xref:core/change-tracking/relationship-changes) zeigt, wie zwei Entitäten durch Nachverfolgen einer neuen Join-Entitäts Instanz zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-137">[Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes) shows how to associate two entities by tracking a new join entity instance.</span></span> <span data-ttu-id="d8d2d-138">Der folgende Code führt dies für den `Dictionary<string, int>` Entitätstyp "Shared" aus, der für die joinentität verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="d8d2d-138">The code below does this for the `Dictionary<string, int>` shared-type entity type used for the join entity:</span></span>

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            var joinEntitySet = context.Set<Dictionary<string, int>>("PostTag");
            var joinEntity = new Dictionary<string, int>
            {
                ["PostId"] = post.Id,
                ["TagId"] = tag.Id
            };
            joinEntitySet.Add(joinEntity);

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);

            context.SaveChanges();
-->
[!code-csharp[DbContext_verses_DbSet_methods_1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/Samples.cs?name=DbContext_verses_DbSet_methods_1)]

<span data-ttu-id="d8d2d-139">Beachten Sie, dass <xref:Microsoft.EntityFrameworkCore.DbContext.Set%60%601(System.String)?displayProperty=nameWithType> verwendet wird, um ein dbset für den `PostTag` Entitätstyp zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-139">Notice that <xref:Microsoft.EntityFrameworkCore.DbContext.Set%60%601(System.String)?displayProperty=nameWithType> is used to create a DbSet for the `PostTag` entity type.</span></span> <span data-ttu-id="d8d2d-140">Dieses dbset kann dann verwendet werden, um `Add` mit der neuen Join-Entitäts Instanz aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-140">This DbSet can then be used to call `Add` with the new join entity instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="d8d2d-141">Der CLR-Typ, der für joinentitäts Typen gemäß der Konvention verwendet wird, kann sich in zukünftigen Versionen ändern, um die Leistung</span><span class="sxs-lookup"><span data-stu-id="d8d2d-141">The CLR type used for join entity types by convention may change in future releases to improve performance.</span></span> <span data-ttu-id="d8d2d-142">Richten Sie sich nicht nach einem bestimmten joinentitätstyp, es sei denn, er wurde explizit so konfiguriert, wie dies für `Dictionary<string, int>` im obigen Code erfolgt ist.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-142">Do not depend on any specific join entity type unless it has been explicitly configured as is done for `Dictionary<string, int>` in the code above.</span></span>

## <a name="property-verses-field-access"></a><span data-ttu-id="d8d2d-143">Eigenschaften-Verse-Feld Zugriff</span><span class="sxs-lookup"><span data-stu-id="d8d2d-143">Property verses field access</span></span>

<span data-ttu-id="d8d2d-144">Ab EF Core 3,0 verwendet der Zugriff auf Entitäts Eigenschaften standardmäßig das Unterstützungs Feld der-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-144">Starting with EF Core 3.0, access to entity properties uses the backing field of the property by default.</span></span> <span data-ttu-id="d8d2d-145">Dies ist effizient und vermeidet das Auslösen von Nebeneffekten aus dem Aufruf von Eigenschaften-Getter und-Setter.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-145">This is efficient and avoids triggering side effects from calling property getters and setters.</span></span> <span data-ttu-id="d8d2d-146">So kann z. b. das verzögerte Laden vermeiden, dass unendliche Schleifen ausgelöst werden.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-146">For example, this is how lazy-loading is able to avoid triggering infinite loops.</span></span> <span data-ttu-id="d8d2d-147">Weitere Informationen zum Konfigurieren von Unterstützungs Feldern im Modell finden Sie unter dahinter liegende [Felder](xref:core/modeling/backing-field) .</span><span class="sxs-lookup"><span data-stu-id="d8d2d-147">See [Backing Fields](xref:core/modeling/backing-field) for more information on configuring backing fields in the model.</span></span>

<span data-ttu-id="d8d2d-148">Manchmal kann es wünschenswert sein, dass EF Core Nebeneffekte generiert, wenn Sie Eigenschaftswerte ändert.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-148">Sometimes it may be desirable for EF Core to generate side-effects when it modifies property values.</span></span> <span data-ttu-id="d8d2d-149">Wenn z. b. beim Binden von Daten an Entitäten eine Eigenschaft festgelegt wird, werden möglicherweise Benachrichtigungen an U.I.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-149">For example, when data binding to entities, setting a property may generate notifications to the U.I.</span></span> <span data-ttu-id="d8d2d-150">Dies geschieht nicht, wenn das Feld direkt festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-150">which do not happen when setting the field directly.</span></span> <span data-ttu-id="d8d2d-151">Dies kann durch Ändern von für erreicht werden <xref:Microsoft.EntityFrameworkCore.PropertyAccessMode> :</span><span class="sxs-lookup"><span data-stu-id="d8d2d-151">This can be achieved by changing the <xref:Microsoft.EntityFrameworkCore.PropertyAccessMode> for:</span></span>

- <span data-ttu-id="d8d2d-152">Alle Entitäts Typen im Modell mit <xref:Microsoft.EntityFrameworkCore.ModelBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType></span><span class="sxs-lookup"><span data-stu-id="d8d2d-152">All entity types in the model using <xref:Microsoft.EntityFrameworkCore.ModelBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType></span></span>
- <span data-ttu-id="d8d2d-153">Alle Eigenschaften und Navigationen eines bestimmten Entitäts Typs mithilfe von <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder%601.UsePropertyAccessMode%2A?displayProperty=nameWithType></span><span class="sxs-lookup"><span data-stu-id="d8d2d-153">All properties and navigations of a specific entity type using <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder%601.UsePropertyAccessMode%2A?displayProperty=nameWithType></span></span>
- <span data-ttu-id="d8d2d-154">Eine bestimmte Eigenschaft mit <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType></span><span class="sxs-lookup"><span data-stu-id="d8d2d-154">A specific property using <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType></span></span>
- <span data-ttu-id="d8d2d-155">Eine bestimmte Navigation mit <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.NavigationBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType></span><span class="sxs-lookup"><span data-stu-id="d8d2d-155">A specific navigation using <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.NavigationBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType></span></span>

<span data-ttu-id="d8d2d-156">Eigenschafts Zugriffs Modi `Field` und `PreferField` bewirken, dass EF Core über das dahinter liegende Feld auf den Eigenschafts Wert zugreift.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-156">Property access modes `Field` and `PreferField` will cause EF Core to access the property value through its backing field.</span></span> <span data-ttu-id="d8d2d-157">Ebenso `Property` bewirken und `PreferProperty` EF Core auf den Eigenschafts Wert über seinen Getter und Setter zugreifen.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-157">Likewise, `Property` and `PreferProperty` will cause EF Core to access the property value through its getter and setter.</span></span>

<span data-ttu-id="d8d2d-158">Wenn `Field` oder `Property` verwendet werden und EF Core nicht auf den Wert über das Feld bzw. die Eigenschaften Getter/Setter zugreifen können, löst EF Core eine Ausnahme aus.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-158">If `Field` or `Property` are used and EF Core cannot access the value through the field or property getter/setter respectively, then EF Core will throw an exception.</span></span> <span data-ttu-id="d8d2d-159">Dadurch wird sichergestellt, dass EF Core immer den Feld-/Eigenschaftenzugriff verwendet, wenn Sie den Eindruck haben</span><span class="sxs-lookup"><span data-stu-id="d8d2d-159">This ensures EF Core is always using field/property access when you think it is.</span></span>

<span data-ttu-id="d8d2d-160">Auf der anderen Seite greifen die `PreferField` Modi und auf `PreferProperty` die Verwendung der Eigenschaft bzw. des dahinter liegenden Felds zurück, wenn es nicht möglich ist, den bevorzugten Zugriff zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-160">On the other hand, the `PreferField` and `PreferProperty` modes will fall back to using the property or backing field respectively if it is not possible to use the preferred access.</span></span> <span data-ttu-id="d8d2d-161">`PreferField` ist der Standardwert von EF Core 3,0 ab.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-161">`PreferField` is the default from EF Core 3.0 onwards.</span></span> <span data-ttu-id="d8d2d-162">Dies bedeutet, dass EF Core immer dann Felder verwendet, wenn es nicht möglich ist, wenn auf eine Eigenschaft über den zugehörigen Getter oder Setter zugegriffen werden muss.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-162">This means EF Core will use fields whenever it can, but will not fail if a property must be accessed through its getter or setter instead.</span></span>

<span data-ttu-id="d8d2d-163">`FieldDuringConstruction` und `PreferFieldDuringConstruction` Konfigurieren EF Core _nur beim Erstellen von Entitäts Instanzen_ für die Verwendung von Unterstützungs Feldern.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-163">`FieldDuringConstruction` and `PreferFieldDuringConstruction` configure EF Core to use of backing fields _only when creating entity instances_.</span></span> <span data-ttu-id="d8d2d-164">Auf diese Weise können Abfragen ohne Getter-und Setter-Nebeneffekte ausgeführt werden, während spätere Eigenschafts Änderungen durch EF Core diese Nebeneffekte verursachen.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-164">This allows queries to be executed without getter and setter side effects, while later property changes by EF Core will cause these side effects.</span></span> <span data-ttu-id="d8d2d-165">`PreferFieldDuringConstruction` war der Standardwert vor EF Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-165">`PreferFieldDuringConstruction` was the default prior to EF Core 3.0.</span></span>

<span data-ttu-id="d8d2d-166">Die verschiedenen Eigenschaften Zugriffs Modi werden in der folgenden Tabelle zusammengefasst:</span><span class="sxs-lookup"><span data-stu-id="d8d2d-166">The different property access modes are summarized in the following table:</span></span>

| <span data-ttu-id="d8d2d-167">Propertyaccessmode</span><span class="sxs-lookup"><span data-stu-id="d8d2d-167">PropertyAccessMode</span></span>              | <span data-ttu-id="d8d2d-168">Bevorzu</span><span class="sxs-lookup"><span data-stu-id="d8d2d-168">Preference</span></span> | <span data-ttu-id="d8d2d-169">Einrichtung von Entitäten bevorzugen</span><span class="sxs-lookup"><span data-stu-id="d8d2d-169">Preference creating entities</span></span> | <span data-ttu-id="d8d2d-170">Fallback</span><span class="sxs-lookup"><span data-stu-id="d8d2d-170">Fallback</span></span> | <span data-ttu-id="d8d2d-171">Fallback beim Erstellen von Entitäten</span><span class="sxs-lookup"><span data-stu-id="d8d2d-171">Fallback creating entities</span></span>
|:--------------------------------|------------|------------------------------|----------|---------------------------
| `Field`                         | <span data-ttu-id="d8d2d-172">Feld</span><span class="sxs-lookup"><span data-stu-id="d8d2d-172">Field</span></span>      | <span data-ttu-id="d8d2d-173">Feld</span><span class="sxs-lookup"><span data-stu-id="d8d2d-173">Field</span></span>                        | <span data-ttu-id="d8d2d-174">Löst aus</span><span class="sxs-lookup"><span data-stu-id="d8d2d-174">Throws</span></span>   | <span data-ttu-id="d8d2d-175">Löst aus</span><span class="sxs-lookup"><span data-stu-id="d8d2d-175">Throws</span></span>
| `Property`                      | <span data-ttu-id="d8d2d-176">Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="d8d2d-176">Property</span></span>   | <span data-ttu-id="d8d2d-177">Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="d8d2d-177">Property</span></span>                     | <span data-ttu-id="d8d2d-178">Löst aus</span><span class="sxs-lookup"><span data-stu-id="d8d2d-178">Throws</span></span>   | <span data-ttu-id="d8d2d-179">Löst aus</span><span class="sxs-lookup"><span data-stu-id="d8d2d-179">Throws</span></span>
| `PreferField`                   | <span data-ttu-id="d8d2d-180">Feld</span><span class="sxs-lookup"><span data-stu-id="d8d2d-180">Field</span></span>      | <span data-ttu-id="d8d2d-181">Feld</span><span class="sxs-lookup"><span data-stu-id="d8d2d-181">Field</span></span>                        | <span data-ttu-id="d8d2d-182">Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="d8d2d-182">Property</span></span> | <span data-ttu-id="d8d2d-183">Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="d8d2d-183">Property</span></span>
| `PreferProperty`                | <span data-ttu-id="d8d2d-184">Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="d8d2d-184">Property</span></span>   | <span data-ttu-id="d8d2d-185">Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="d8d2d-185">Property</span></span>                     | <span data-ttu-id="d8d2d-186">Feld</span><span class="sxs-lookup"><span data-stu-id="d8d2d-186">Field</span></span>    | <span data-ttu-id="d8d2d-187">Feld</span><span class="sxs-lookup"><span data-stu-id="d8d2d-187">Field</span></span>
| `FieldDuringConstruction`       | <span data-ttu-id="d8d2d-188">Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="d8d2d-188">Property</span></span>   | <span data-ttu-id="d8d2d-189">Feld</span><span class="sxs-lookup"><span data-stu-id="d8d2d-189">Field</span></span>                        | <span data-ttu-id="d8d2d-190">Feld</span><span class="sxs-lookup"><span data-stu-id="d8d2d-190">Field</span></span>    | <span data-ttu-id="d8d2d-191">Löst aus</span><span class="sxs-lookup"><span data-stu-id="d8d2d-191">Throws</span></span>
| `PreferFieldDuringConstruction` | <span data-ttu-id="d8d2d-192">Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="d8d2d-192">Property</span></span>   | <span data-ttu-id="d8d2d-193">Feld</span><span class="sxs-lookup"><span data-stu-id="d8d2d-193">Field</span></span>                        | <span data-ttu-id="d8d2d-194">Feld</span><span class="sxs-lookup"><span data-stu-id="d8d2d-194">Field</span></span>    | <span data-ttu-id="d8d2d-195">Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="d8d2d-195">Property</span></span>

## <a name="temporary-values"></a><span data-ttu-id="d8d2d-196">Temporäre Werte</span><span class="sxs-lookup"><span data-stu-id="d8d2d-196">Temporary values</span></span>

<span data-ttu-id="d8d2d-197">EF Core erstellt temporäre Schlüsselwerte, wenn neue Entitäten nachverfolgt werden, deren echte Schlüsselwerte von der Datenbank generiert werden, wenn SaveChanges aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-197">EF Core creates temporary key values when tracking new entities that will have real key values generated by the database when SaveChanges is called.</span></span> <span data-ttu-id="d8d2d-198">Eine Übersicht über die Verwendung dieser temporären Werte finden Sie [unter Änderungsnachverfolgung in EF Core](xref:core/change-tracking/index) .</span><span class="sxs-lookup"><span data-stu-id="d8d2d-198">See [Change Tracking in EF Core](xref:core/change-tracking/index) for an overview of how these temporary values are used.</span></span>

### <a name="accessing-temporary-values"></a><span data-ttu-id="d8d2d-199">Zugreifen auf temporäre Werte</span><span class="sxs-lookup"><span data-stu-id="d8d2d-199">Accessing temporary values</span></span>

<span data-ttu-id="d8d2d-200">Ab EF Core 3,0 werden temporäre Werte in der Änderungs Protokollierung gespeichert und nicht direkt auf Entitäts Instanzen festgelegt.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-200">Starting with EF Core 3.0, temporary values are stored in the change tracker and not set onto entity instances directly.</span></span> <span data-ttu-id="d8d2d-201">Diese temporären Werte werden jedoch verfügbar gemacht, wenn die verschiedenen Mechanismen für den [Zugriff auf verfolgte Entitäten](xref:core/change-tracking/entity-entries)verwendet _werden_ .</span><span class="sxs-lookup"><span data-stu-id="d8d2d-201">However, these temporary values _are_ exposed when using the various mechanisms for [Accessing Tracked Entities](xref:core/change-tracking/entity-entries).</span></span> <span data-ttu-id="d8d2d-202">Beispielsweise greift der folgende Code mithilfe von auf einen temporären Wert zu <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.CurrentValues%2A?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="d8d2d-202">For example, the following code accesses a temporary value using <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.CurrentValues%2A?displayProperty=nameWithType>:</span></span>

<!--
        using var context = new BlogsContext();

        var blog = new Blog { Name = ".NET Blog" };

        context.Add(blog);

        Console.WriteLine($"Blog.Id set on entity is {blog.Id}");
        Console.WriteLine($"Blog.Id tracked by EF is {context.Entry(blog).Property(e => e.Id).CurrentValue}");
-->
[!code-csharp[Temporary_values_1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/Samples.cs?name=Temporary_values_1)]

<span data-ttu-id="d8d2d-203">Die Ausgabe dieses Codes lautet wie folgt:</span><span class="sxs-lookup"><span data-stu-id="d8d2d-203">The output from this code is:</span></span>

```output
Blog.Id set on entity is 0
Blog.Id tracked by EF is -2147482643
```

<span data-ttu-id="d8d2d-204"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary%2A?displayProperty=nameWithType> kann verwendet werden, um nach temporären Werten zu suchen.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-204"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary%2A?displayProperty=nameWithType> can be used to check for temporary values.</span></span>

### <a name="manipulating-temporary-values"></a><span data-ttu-id="d8d2d-205">Bearbeiten temporärer Werte</span><span class="sxs-lookup"><span data-stu-id="d8d2d-205">Manipulating temporary values</span></span>

<span data-ttu-id="d8d2d-206">Es ist manchmal hilfreich, explizit mit temporären Werten zu arbeiten.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-206">It is sometimes useful to explicitly work with temporary values.</span></span> <span data-ttu-id="d8d2d-207">Beispielsweise kann eine Auflistung von neuen Entitäten auf einem Webclient erstellt und dann zurück zum Server serialisiert werden.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-207">For example, a collection of new entities might be created on a web client and then serialized back to the server.</span></span> <span data-ttu-id="d8d2d-208">Fremdschlüssel Werte sind eine Möglichkeit zum Einrichten von Beziehungen zwischen diesen Entitäten.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-208">Foreign key values are one way to set up relationships between these entities.</span></span> <span data-ttu-id="d8d2d-209">Der folgende Code verwendet diesen Ansatz, um ein Diagramm neuer Entitäten nach Fremdschlüssel zuzuordnen, während gleichzeitig beim Aufrufen von SaveChanges echte Schlüsselwerte generiert werden können.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-209">The following code uses this approach to associate a graph of new entities by foreign key, while still allowing real key values to be generated when SaveChanges is called.</span></span>

<!--
            var blogs = new List<Blog>
            {
                new Blog { Id = -1, Name = ".NET Blog" },
                new Blog { Id = -2, Name = "Visual Studio Blog" }
            };

            var posts = new List<Post>()
            {
                new Post
                {
                    Id = -1,
                    BlogId = -1,
                    Title = "Announcing the Release of EF Core 5.0",
                    Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                },
                new Post
                {
                    Id = -2,
                    BlogId = -2,
                    Title = "Disassembly improvements for optimized managed debugging",
                    Content = "If you are focused on squeezing out the last bits of performance for your .NET service or..."
                }
            };

            using var context = new BlogsContext();

            foreach (var blog in blogs)
            {
                context.Add(blog).Property(e => e.Id).IsTemporary = true;
            }

            foreach (var post in posts)
            {
                context.Add(post).Property(e => e.Id).IsTemporary = true;
            }

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Temporary_values_2](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/Samples.cs?name=Temporary_values_2)]

<span data-ttu-id="d8d2d-210">Beachten Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="d8d2d-210">Notice that:</span></span>

- <span data-ttu-id="d8d2d-211">Negative Zahlen werden als temporäre Schlüsselwerte verwendet. Dies ist nicht erforderlich, ist jedoch eine gängige Konvention, um Schlüssel Konflikte zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-211">Negative numbers are used as temporary key values; this is not required, but is a common convention to prevent key clashes.</span></span>
- <span data-ttu-id="d8d2d-212">Der `Post.BlogId` Eigenschaft "FK" wird der gleiche negative Wert wie der PK des zugehörigen Blogs zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-212">The `Post.BlogId` FK property is assigned the same negative value as the PK of the associated blog.</span></span>
- <span data-ttu-id="d8d2d-213">Die PK-Werte werden als temporär gekennzeichnet, indem Sie festlegen, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary%2A> nachdem jede Entität nachverfolgt wird.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-213">The PK values are marked as temporary by setting <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary%2A> after each entity is tracked.</span></span> <span data-ttu-id="d8d2d-214">Dies ist erforderlich, da davon ausgegangen wird, dass jeder von der Anwendung bereitgestellte Schlüsselwert ein echter Schlüsselwert ist.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-214">This is necessary because any key value supplied by the application is assumed to be a real key value.</span></span>

<span data-ttu-id="d8d2d-215">Wenn Sie vor dem Aufrufen von SaveChanges die [Debugansicht](xref:core/change-tracking/debug-views) für die Änderungs Nachverfolgung anzeigen, sehen Sie, dass die PK-Werte als temporär gekennzeichnet sind und Beiträge mit den richtigen Blogs verknüpft sind</span><span class="sxs-lookup"><span data-stu-id="d8d2d-215">Looking at the [change tracker debug view](xref:core/change-tracking/debug-views) before calling SaveChanges shows that the PK values are marked as temporary and posts are associated with the correct blogs, including fixup of navigations:</span></span>

```output
Blog {Id: -2} Added
  Id: -2 PK Temporary
  Name: 'Visual Studio Blog'
  Posts: [{Id: -2}]
Blog {Id: -1} Added
  Id: -1 PK Temporary
  Name: '.NET Blog'
  Posts: [{Id: -1}]
Post {Id: -2} Added
  Id: -2 PK Temporary
  BlogId: -2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: -2}
  Tags: []
Post {Id: -1} Added
  Id: -1 PK Temporary
  BlogId: -1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: -1}
```

<span data-ttu-id="d8d2d-216">Nach <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> dem Aufruf von wurden diese temporären Werte durch von der Datenbank generierte reale Werte ersetzt:</span><span class="sxs-lookup"><span data-stu-id="d8d2d-216">After calling <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A>, these temporary values have been replaced by real values generated by the database:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}]
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Posts: [{Id: 2}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 2}
  Tags: []
```

## <a name="working-with-default-values"></a><span data-ttu-id="d8d2d-217">Arbeiten mit Standardwerten</span><span class="sxs-lookup"><span data-stu-id="d8d2d-217">Working with default values</span></span>

<span data-ttu-id="d8d2d-218">EF Core ermöglicht es einer Eigenschaft, ihren Standardwert aus der Datenbank zu erhalten, wenn <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-218">EF Core allows a property to get its default value from the database when <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> is called.</span></span> <span data-ttu-id="d8d2d-219">Wie bei generierten Schlüsselwerten wird EF Core nur einen Standardwert aus der Datenbank verwenden, wenn kein Wert explizit festgelegt wurde.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-219">Like with generated key values, EF Core will only use a default from the database if no value has been explicitly set.</span></span> <span data-ttu-id="d8d2d-220">Sehen Sie sich beispielsweise den folgenden Entitätstyp an:</span><span class="sxs-lookup"><span data-stu-id="d8d2d-220">For example, consider the following entity type:</span></span>

<!--
    public class Token
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public DateTime ValidFrom { get; set; }
    }
-->
[!code-csharp[Token](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Token)]

<span data-ttu-id="d8d2d-221">Die- `ValidFrom` Eigenschaft ist so konfiguriert, dass Sie einen Standardwert aus der Datenbank erhält:</span><span class="sxs-lookup"><span data-stu-id="d8d2d-221">The `ValidFrom` property is configured to get a default value from the database:</span></span>

<!--
        modelBuilder
            .Entity<Token>()
            .Property(e => e.ValidFrom)
            .HasDefaultValueSql("CURRENT_TIMESTAMP");
-->
[!code-csharp[OnModelCreating_Token](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=OnModelCreating_Token)]

<span data-ttu-id="d8d2d-222">Wenn eine Entität dieses Typs eingefügt wird, ermöglicht EF Core der Datenbank, den Wert zu generieren, es sei denn, stattdessen wurde ein expliziter Wert festgelegt.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-222">When inserting an entity of this type, EF Core will let the database generate the value unless an explicit value has been set instead.</span></span> <span data-ttu-id="d8d2d-223">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d8d2d-223">For example:</span></span>

<!--
            using var context = new BlogsContext();

            context.AddRange(
                new Token { Name = "A" },
                new Token { Name = "B", ValidFrom = new DateTime(1111, 11, 11, 11, 11, 11)});

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Working_with_default_values_1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_1)]

<span data-ttu-id="d8d2d-224">Wenn Sie die [Debugansicht der Änderungs](xref:core/change-tracking/debug-views) Nachverfolgung anzeigen, wird angezeigt, dass das erste Token `ValidFrom` von der Datenbank generiert wurde, während das zweite Token den explizit festgelegten Wert verwendet hat:</span><span class="sxs-lookup"><span data-stu-id="d8d2d-224">Looking at the [change tracker debug view](xref:core/change-tracking/debug-views) shows that the first token had `ValidFrom` generated by the database, while the second token used the value explicitly set:</span></span>

```output
Token {Id: 1} Unchanged
  Id: 1 PK
  Name: 'A'
  ValidFrom: '12/30/2020 6:36:06 PM'
Token {Id: 2} Unchanged
  Id: 2 PK
  Name: 'B'
  ValidFrom: '11/11/1111 11:11:11 AM'
```

> [!NOTE]
> <span data-ttu-id="d8d2d-225">Die Verwendung von Daten Bank Standardwerten erfordert, dass für die Daten Bank Spalte eine Standardwert Einschränkung konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-225">Using database default values requires that the database column has a default value constraint configured.</span></span> <span data-ttu-id="d8d2d-226">Dies erfolgt automatisch durch EF Core Migrationen, wenn <xref:Microsoft.EntityFrameworkCore.RelationalPropertyBuilderExtensions.HasDefaultValueSql%2A> oder verwendet wird <xref:Microsoft.EntityFrameworkCore.RelationalPropertyBuilderExtensions.HasDefaultValue%2A> .</span><span class="sxs-lookup"><span data-stu-id="d8d2d-226">This is done automatically by EF Core migrations when using <xref:Microsoft.EntityFrameworkCore.RelationalPropertyBuilderExtensions.HasDefaultValueSql%2A> or <xref:Microsoft.EntityFrameworkCore.RelationalPropertyBuilderExtensions.HasDefaultValue%2A>.</span></span> <span data-ttu-id="d8d2d-227">Stellen Sie sicher, dass Sie die DEFAULT-Einschränkung für die Spalte auf andere Weise erstellen, wenn Sie keine EF Core Migrationen verwenden.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-227">Make sure to create the default constraint on the column in some other way when not using EF Core migrations.</span></span>

### <a name="using-nullable-properties"></a><span data-ttu-id="d8d2d-228">Verwenden von Werte zulässt-Eigenschaften</span><span class="sxs-lookup"><span data-stu-id="d8d2d-228">Using nullable properties</span></span>

<span data-ttu-id="d8d2d-229">EF Core kann ermitteln, ob eine Eigenschaft festgelegt wurde, indem Sie den Eigenschafts Wert mit dem CLR-Standardwert für den betreffenden Typ vergleicht.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-229">EF Core is able to determine whether or not a property has been set by comparing the property value to the CLR default for the that type.</span></span> <span data-ttu-id="d8d2d-230">Dies funktioniert in den meisten Fällen gut, bedeutet aber, dass die CLR-Standardeinstellung nicht explizit in die Datenbank eingefügt werden kann.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-230">This works well in most cases, but means that the CLR default cannot be explicitly inserted into the database.</span></span> <span data-ttu-id="d8d2d-231">Stellen Sie sich beispielsweise eine Entität mit einer ganzzahligen Eigenschaft vor:</span><span class="sxs-lookup"><span data-stu-id="d8d2d-231">For example, consider an entity with an integer property:</span></span>

<!--
public class Foo1
{
    public int Id { get; set; }
    public int Count { get; set; }
}
-->
[!code-csharp[Foo1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Foo1)]

<span data-ttu-id="d8d2d-232">Wenn diese Eigenschaft für den Daten Bank Standardwert-1 konfiguriert ist:</span><span class="sxs-lookup"><span data-stu-id="d8d2d-232">Where that property is configured to have a database default of -1:</span></span>

<!--
        modelBuilder
            .Entity<Foo1>()
            .Property(e => e.Count)
            .HasDefaultValue(-1);
-->
[!code-csharp[OnModelCreating_Foo1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=OnModelCreating_Foo1)]

<span data-ttu-id="d8d2d-233">Die Absicht ist, dass der Standardwert-1 verwendet wird, wenn kein expliziter Wert festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-233">The intention is that the default of -1 will be used whenever an explicit value is not set.</span></span> <span data-ttu-id="d8d2d-234">Das Festlegen des Werts auf 0 (die CLR-Standardeinstellung für ganze Zahlen) kann jedoch nicht unterschieden werden, EF Core kein Wert festgelegt wird. Dies bedeutet, dass es nicht möglich ist, 0 für diese Eigenschaft einzufügen.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-234">However, setting the value to 0 (the CLR default for integers) is indistinguishable to EF Core from not setting any value, this means that it is not possible to insert 0 for this property.</span></span> <span data-ttu-id="d8d2d-235">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d8d2d-235">For example:</span></span>

<!--
        using var context = new BlogsContext();

        var fooA = new Foo1 { Count = 10 };
        var fooB = new Foo1 { Count = 0 };
        var fooC = new Foo1 { };

        context.AddRange(fooA, fooB, fooC);
        context.SaveChanges();

        Debug.Assert(fooA.Count == 10);
        Debug.Assert(fooB.Count == -1); // Not what we want!
        Debug.Assert(fooC.Count == -1);
-->
[!code-csharp[Working_with_default_values_2](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_2)]

<span data-ttu-id="d8d2d-236">Beachten Sie, dass die-Instanz, bei der `Count` explizit auf 0 festgelegt wurde, immer noch den Standardwert aus der Datenbank erhält. Dies ist nicht beabsichtigt.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-236">Notice that the instance where `Count` was explicitly set to 0 is still gets the default value from the database, which is not what we intended.</span></span> <span data-ttu-id="d8d2d-237">Eine einfache Möglichkeit, dies zu umgehen, besteht darin, dass die `Count` Eigenschaft NULL-Werte zulässt:</span><span class="sxs-lookup"><span data-stu-id="d8d2d-237">An easy way to deal with this is to make the `Count` property nullable:</span></span>

<!--
public class Foo2
{
    public int Id { get; set; }
    public int? Count { get; set; }
}
-->
[!code-csharp[Foo2](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Foo2)]

<span data-ttu-id="d8d2d-238">Dadurch wird der CLR-Standardwert NULL (anstelle von 0), d. h., wenn er explizit festgelegt wird, wird 0 eingefügt:</span><span class="sxs-lookup"><span data-stu-id="d8d2d-238">This makes the CLR default null, instead of 0, which means 0 will now be inserted when explicitly set:</span></span>

<!--
        using var context = new BlogsContext();

        var fooA = new Foo2 { Count = 10 };
        var fooB = new Foo2 { Count = 0 };
        var fooC = new Foo2 { };

        context.AddRange(fooA, fooB, fooC);
        context.SaveChanges();

        Debug.Assert(fooA.Count == 10);
        Debug.Assert(fooB.Count == 0);
        Debug.Assert(fooC.Count == -1);
-->
[!code-csharp[Working_with_default_values_3](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_3)]

### <a name="using-nullable-backing-fields"></a><span data-ttu-id="d8d2d-239">Verwenden von Werte zulässt-Unterstützungs Feldern</span><span class="sxs-lookup"><span data-stu-id="d8d2d-239">Using nullable backing fields</span></span>

> [!NOTE]
> <span data-ttu-id="d8d2d-240">Dieses Unterstützungs Feld Muster, das NULL-Werte zulässt, wird von EF Core 5,0 und höher unterstützt.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-240">This nullable backing field pattern is supported by EF Core 5.0 and later.</span></span>

<span data-ttu-id="d8d2d-241">Das Problem, dass die Eigenschaft auf NULL festgelegt werden kann, kann im Domänen Modell nicht konzeptionell NULL sein.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-241">The problem with making the property nullable that it may not be conceptually nullable in the domain model.</span></span> <span data-ttu-id="d8d2d-242">Wenn Sie erzwingen, dass die Eigenschaft auf NULL festgelegt werden kann, wird das Modell gefährdet</span><span class="sxs-lookup"><span data-stu-id="d8d2d-242">Forcing the property to be nullable therefore compromises the model.</span></span>

<span data-ttu-id="d8d2d-243">Ab EF Core 5,0 kann die-Eigenschaft nicht auf NULL festgelegt werden, und nur das Unterstützungs Feld lässt NULL-Werte zu.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-243">Starting with EF Core 5.0, the property can be left non-nullable, with only the backing field being nullable.</span></span> <span data-ttu-id="d8d2d-244">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d8d2d-244">For example:</span></span>

<!--
public class Foo3
{
    public int Id { get; set; }

    private int? _count;
    public int Count
    {
        get => _count ?? -1;
        set => _count = value;
    }
}
-->
[!code-csharp[Foo3](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Foo3)]

<span data-ttu-id="d8d2d-245">Dadurch kann die CLR-Standardeinstellung (0) eingefügt werden, wenn die-Eigenschaft explizit auf 0 festgelegt ist, während die-Eigenschaft im Domänen Modell nicht als Werte zulässt verfügbar gemacht werden muss.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-245">This allows the CLR default (0) to be inserted if the property is explicitly set to 0, while not needing to expose the property as nullable in the domain model.</span></span> <span data-ttu-id="d8d2d-246">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d8d2d-246">For example:</span></span>

<!--
            using var context = new BlogsContext();

            var fooA = new Foo3 { Count = 10 };
            var fooB = new Foo3 { Count = 0 };
            var fooC = new Foo3 { };

            context.AddRange(fooA, fooB, fooC);
            context.SaveChanges();

            Debug.Assert(fooA.Count == 10);
            Debug.Assert(fooB.Count == 0);
            Debug.Assert(fooC.Count == -1);
-->
[!code-csharp[Working_with_default_values_4](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_4)]

#### <a name="nullable-backing-fields-for-bool-properties"></a><span data-ttu-id="d8d2d-247">Auf NULL festleg Bare Unterstützungs Felder für boolesche Eigenschaften</span><span class="sxs-lookup"><span data-stu-id="d8d2d-247">Nullable backing fields for bool properties</span></span>

<span data-ttu-id="d8d2d-248">Dieses Muster ist besonders nützlich bei der Verwendung von booleschen Eigenschaften mit vom Speicher generierten Standardwerten.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-248">This pattern is especially useful when using bool properties with store-generated defaults.</span></span> <span data-ttu-id="d8d2d-249">Da der CLR-Standard für den Wert `bool` "false" hat, bedeutet dies, dass "false" nicht explizit mit dem normalen Muster eingefügt werden kann.</span><span class="sxs-lookup"><span data-stu-id="d8d2d-249">Since the CLR default for `bool` is "false", it means that "false" cannot be inserted explicitly using the normal pattern.</span></span> <span data-ttu-id="d8d2d-250">Nehmen Sie beispielsweise einen `User` Entitätstyp an:</span><span class="sxs-lookup"><span data-stu-id="d8d2d-250">For example, consider a `User` entity type:</span></span>

<!--
public class User
{
    public int Id { get; set; }
    public string Name { get; set; }

    private bool? _isAuthorized;
    public bool IsAuthorized
    {
        get => _isAuthorized ?? true;
        set => _isAuthorized = value;
    }
}
-->
[!code-csharp[User](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=User)]

<span data-ttu-id="d8d2d-251">Die- `IsAuthorized` Eigenschaft wird mit dem Standardwert "true" der Datenbank konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="d8d2d-251">The `IsAuthorized` property is configured with a database default value of "true":</span></span>

<!--
        modelBuilder
            .Entity<User>()
            .Property(e => e.IsAuthorized)
            .HasDefaultValue(true);
-->
[!code-csharp[OnModelCreating_User](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=OnModelCreating_User)]

<span data-ttu-id="d8d2d-252">Die `IsAuthorized` Eigenschaft kann vor dem Einfügen explizit auf "true" oder "false" festgelegt werden, oder die Eigenschaft kann nicht festgelegt werden. in diesem Fall wird der Daten Bank Standard verwendet:</span><span class="sxs-lookup"><span data-stu-id="d8d2d-252">The `IsAuthorized` property can be set to "true" or "false" explicitly before inserting, or can be left unset in which case the database default will be used:</span></span>

<!--
        using var context = new BlogsContext();

        var userA = new User { Name = "Mac" };
        var userB = new User { Name = "Alice", IsAuthorized = true };
        var userC = new User { Name = "Baxter", IsAuthorized = false }; // Always deny Baxter access!

        context.AddRange(userA, userB, userC);

        context.SaveChanges();
-->
[!code-csharp[Working_with_default_values_5](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_5)]

<span data-ttu-id="d8d2d-253">Die Ausgabe von SaveChanges bei der Verwendung von SQLite zeigt, dass der Daten Bank Standard für Mac verwendet wird, während für Alice und Baxter explizite Werte festgelegt sind:</span><span class="sxs-lookup"><span data-stu-id="d8d2d-253">The output from SaveChanges when using SQLite shows that the database default is used for Mac, while explicit values are set for Alice and Baxter:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='Mac' (Size = 3)], CommandType='Text', CommandTimeout='30']
INSERT INTO "User" ("Name")
VALUES (@p0);
SELECT "Id", "IsAuthorized"
FROM "User"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();

-- Executed DbCommand (0ms) [Parameters=[@p0='True' (DbType = String), @p1='Alice' (Size = 5)], CommandType='Text', CommandTimeout='30']
INSERT INTO "User" ("IsAuthorized", "Name")
VALUES (@p0, @p1);
SELECT "Id"
FROM "User"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();

-- Executed DbCommand (0ms) [Parameters=[@p0='False' (DbType = String), @p1='Baxter' (Size = 6)], CommandType='Text', CommandTimeout='30']
INSERT INTO "User" ("IsAuthorized", "Name")
VALUES (@p0, @p1);
SELECT "Id"
FROM "User"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

### <a name="schema-defaults-only"></a><span data-ttu-id="d8d2d-254">Nur Schema Standardwerte</span><span class="sxs-lookup"><span data-stu-id="d8d2d-254">Schema defaults only</span></span>

<span data-ttu-id="d8d2d-255">In manchen Fällen ist es sinnvoll, die Standardeinstellungen des Datenbankschemas zu verwenden, das durch EF Core Migrationen erstellt wurde, EF Core ohne diese Werte für Einfügungen</span><span class="sxs-lookup"><span data-stu-id="d8d2d-255">Sometimes it is useful to have defaults in the database schema created by EF Core migrations without EF Core ever using these values for inserts.</span></span> <span data-ttu-id="d8d2d-256">Dies kann erreicht werden, indem Sie die-Eigenschaft wie folgt konfigurieren <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.ValueGeneratedNever%2A?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="d8d2d-256">This can be achieved by configuring the property as <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.ValueGeneratedNever%2A?displayProperty=nameWithType> For example:</span></span>

<!--
        modelBuilder
            .Entity<Bar>()
            .Property(e => e.Count)
            .HasDefaultValue(-1)
            .ValueGeneratedNever();
-->
[!code-csharp[OnModelCreating_Bar](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=OnModelCreating_Bar)]
