---
title: Wert Konvertierungen-EF Core
description: Konfigurieren von Wert Konvertern in einem Entity Framework Core Modell
author: ajcvickers
ms.date: 01/16/2021
uid: core/modeling/value-conversions
ms.openlocfilehash: d9d3753c7f0b257a2109e4af1f587df913c15b44
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983442"
---
# <a name="value-conversions"></a><span data-ttu-id="16fd0-103">Wertkonvertierungen</span><span class="sxs-lookup"><span data-stu-id="16fd0-103">Value Conversions</span></span>

<span data-ttu-id="16fd0-104">Mithilfe von Wert Konvertern können Eigenschaftswerte beim Lesen aus der Datenbank oder beim Schreiben in die Datenbank konvertiert werden.</span><span class="sxs-lookup"><span data-stu-id="16fd0-104">Value converters allow property values to be converted when reading from or writing to the database.</span></span> <span data-ttu-id="16fd0-105">Diese Konvertierung kann von einem Wert in einen anderen desselben Typs (z. b. das Verschlüsseln von Zeichen folgen) oder von einem Wert eines Typs zu einem Wert eines anderen Typs erfolgen (z. b. beim Konvertieren von Enumerationswerten in und aus Zeichen folgen in der Datenbank).</span><span class="sxs-lookup"><span data-stu-id="16fd0-105">This conversion can be from one value to another of the same type (for example, encrypting strings) or from a value of one type to a value of another type (for example, converting enum values to and from strings in the database.)</span></span>

> [!TIP]
> <span data-ttu-id="16fd0-106">Sie können den gesamten Code in dieser Dokumentation ausführen und debuggen, indem Sie [den Beispielcode von GitHub herunterladen](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/).</span><span class="sxs-lookup"><span data-stu-id="16fd0-106">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/).</span></span>

## <a name="overview"></a><span data-ttu-id="16fd0-107">Übersicht</span><span class="sxs-lookup"><span data-stu-id="16fd0-107">Overview</span></span>

<span data-ttu-id="16fd0-108">Wert Konverter werden in Bezug auf einen `ModelClrType` und einen angegeben `ProviderClrType` .</span><span class="sxs-lookup"><span data-stu-id="16fd0-108">Value converters are specified in terms of a `ModelClrType` and a `ProviderClrType`.</span></span> <span data-ttu-id="16fd0-109">Der Modelltyp ist der .NET-Typ der-Eigenschaft im-Entitätstyp.</span><span class="sxs-lookup"><span data-stu-id="16fd0-109">The model type is the .NET type of the property in the entity type.</span></span> <span data-ttu-id="16fd0-110">Der Anbietertyp ist der .NET-Typ, der vom Datenbankanbieter verstanden wird.</span><span class="sxs-lookup"><span data-stu-id="16fd0-110">The provider type is the .NET type understood by the database provider.</span></span> <span data-ttu-id="16fd0-111">Um z. b. Enumerationen als Zeichen folgen in der Datenbank zu speichern, ist der Modelltyp der Typ der Enumeration, und der Anbietertyp ist `String` .</span><span class="sxs-lookup"><span data-stu-id="16fd0-111">For example, to save enums as strings in the database, the model type is the type of the enum, and the provider type is `String`.</span></span> <span data-ttu-id="16fd0-112">Diese beiden Typen können identisch sein.</span><span class="sxs-lookup"><span data-stu-id="16fd0-112">These two types can be the same.</span></span>

<span data-ttu-id="16fd0-113">Konvertierungen werden mithilfe `Func` von zwei Ausdrucks Baumstrukturen definiert: einer von `ModelClrType` `ProviderClrType` und der andere von `ProviderClrType` bis `ModelClrType` .</span><span class="sxs-lookup"><span data-stu-id="16fd0-113">Conversions are defined using two `Func` expression trees: one from `ModelClrType` to `ProviderClrType` and the other from `ProviderClrType` to `ModelClrType`.</span></span> <span data-ttu-id="16fd0-114">Ausdrucks Baumstrukturen werden verwendet, damit Sie für effiziente Konvertierungen in den Datenbankzugriffs Delegaten kompiliert werden können.</span><span class="sxs-lookup"><span data-stu-id="16fd0-114">Expression trees are used so that they can be compiled into the database access delegate for efficient conversions.</span></span> <span data-ttu-id="16fd0-115">Die Ausdrucks Baumstruktur kann einen einfachen aufrufungs Methode für komplexe Konvertierungen enthalten.</span><span class="sxs-lookup"><span data-stu-id="16fd0-115">The expression tree may contain a simple call to a conversion method for complex conversions.</span></span>

> [!NOTE]
> <span data-ttu-id="16fd0-116">Eine Eigenschaft, die für die Wert Konvertierung konfiguriert wurde, muss möglicherweise auch ein angeben <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> .</span><span class="sxs-lookup"><span data-stu-id="16fd0-116">A property that has been configured for value conversion may also need to specify a <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601>.</span></span> <span data-ttu-id="16fd0-117">Weitere Informationen finden Sie in den Beispielen unten und in der Dokumentation zu den [Wert Comparer](xref:core/modeling/value-comparers) .</span><span class="sxs-lookup"><span data-stu-id="16fd0-117">See the examples below, and the [Value Comparers](xref:core/modeling/value-comparers) documentation for more information.</span></span>

## <a name="configuring-a-value-converter"></a><span data-ttu-id="16fd0-118">Konfigurieren eines Wert Konverters</span><span class="sxs-lookup"><span data-stu-id="16fd0-118">Configuring a value converter</span></span>

<span data-ttu-id="16fd0-119">Wert Konvertierungen werden in konfiguriert <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="16fd0-119">Value conversions are configured in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="16fd0-120">Stellen Sie sich z. b. eine Aufzählung und einen Entitätstyp vor</span><span class="sxs-lookup"><span data-stu-id="16fd0-120">For example, consider an enum and entity type defined as:</span></span>

<!--
        public class Rider
        {
            public int Id { get; set; }
            public EquineBeast Mount { get; set; }
        }

        public enum EquineBeast
        {
            Donkey,
            Mule,
            Horse,
            Unicorn
        }
-->
[!code-csharp[BeastAndRider](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=BeastAndRider)]

<span data-ttu-id="16fd0-121">Konvertierungen können in so konfiguriert werden, <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> dass die Enumerationswerte als Zeichen folgen wie "Esel", "maulzeichen" usw. in der Datenbank gespeichert werden. Sie müssen lediglich eine Funktion bereitstellen, die aus dem in `ModelClrType` den konvertiert `ProviderClrType` und eine andere für die umgekehrte Konvertierung:</span><span class="sxs-lookup"><span data-stu-id="16fd0-121">Conversions can be configured in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> to store the enum values as strings such as "Donkey", "Mule", etc. in the database; you simply need to provide one function which converts from the `ModelClrType` to the `ProviderClrType`, and another for the opposite conversion:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                modelBuilder
                    .Entity<Rider>()
                    .Property(e => e.Mount)
                    .HasConversion(
                        v => v.ToString(),
                        v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));
            }
-->
[!code-csharp[ExplicitConversion](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ExplicitConversion)]

> [!NOTE]
> <span data-ttu-id="16fd0-122">Ein `null` Wert wird nie an einen Wert Konverter übermittelt.</span><span class="sxs-lookup"><span data-stu-id="16fd0-122">A `null` value will never be passed to a value converter.</span></span> <span data-ttu-id="16fd0-123">Ein NULL-Wert in einer Daten Bank Spalte ist in der Entitäts Instanz immer NULL (und umgekehrt).</span><span class="sxs-lookup"><span data-stu-id="16fd0-123">A null in a database column is always a null in the entity instance, and vice-versa.</span></span> <span data-ttu-id="16fd0-124">Dies vereinfacht die Implementierung von Konvertierungen und ermöglicht die gemeinsame Nutzung zwischen NULL-Werten und nicht auf NULL festleg baren Eigenschaften.</span><span class="sxs-lookup"><span data-stu-id="16fd0-124">This makes the implementation of conversions easier and allows them to be shared amongst nullable and non-nullable properties.</span></span> <span data-ttu-id="16fd0-125">Weitere Informationen finden Sie unter [GitHub-Problem #13850](https://github.com/dotnet/efcore/issues/13850) .</span><span class="sxs-lookup"><span data-stu-id="16fd0-125">See [GitHub issue #13850](https://github.com/dotnet/efcore/issues/13850) for more information.</span></span>

## <a name="pre-defined-conversions"></a><span data-ttu-id="16fd0-126">Vordefinierte Konvertierungen</span><span class="sxs-lookup"><span data-stu-id="16fd0-126">Pre-defined conversions</span></span>

<span data-ttu-id="16fd0-127">EF Core enthält viele vordefinierte Konvertierungen, die das manuelle Schreiben von Konvertierungs Funktionen vermeiden.</span><span class="sxs-lookup"><span data-stu-id="16fd0-127">EF Core contains many pre-defined conversions that avoid the need to write conversion functions manually.</span></span> <span data-ttu-id="16fd0-128">Stattdessen wählt EF Core die Konvertierung aus, die basierend auf dem Eigenschaftentyp im Modell und dem angeforderten Daten Bank Anbietertyp verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="16fd0-128">Instead, EF Core will pick the conversion to use based on the property type in the model and the requested database provider type.</span></span>

<span data-ttu-id="16fd0-129">So werden z. b. Enumeration-zu-Zeichen folgen Konvertierungen als Beispiel verwendet, aber EF Core wird dies automatisch durchführen, wenn der Anbietertyp `string` mit dem generischen Typ von konfiguriert ist <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.HasConversion%2A> :</span><span class="sxs-lookup"><span data-stu-id="16fd0-129">For example, enum to string conversions are used as an example above, but EF Core will actually do this automatically when the provider type is configured as `string` using the generic type of <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.HasConversion%2A>:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                modelBuilder
                    .Entity<Rider>()
                    .Property(e => e.Mount)
                    .HasConversion<string>();
            }
-->
[!code-csharp[ConversionByClrType](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByClrType)]

<span data-ttu-id="16fd0-130">Dies kann auch durch explizites angeben des Daten Bank Spalten Typs erreicht werden.</span><span class="sxs-lookup"><span data-stu-id="16fd0-130">The same thing can be achieved by explicitly specifying the database column type.</span></span> <span data-ttu-id="16fd0-131">Beispielsweise, wenn der Entitätstyp wie folgt definiert ist:</span><span class="sxs-lookup"><span data-stu-id="16fd0-131">For example, if the entity type is defined like so:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="16fd0-132">Daten Anmerkungen</span><span class="sxs-lookup"><span data-stu-id="16fd0-132">Data Annotations</span></span>](#tab/data-annotations)

<!--
        public class Rider2
        {
            public int Id { get; set; }

            [Column(TypeName = "nvarchar(24)")]
            public EquineBeast Mount { get; set; }
        }
-->
[!code-csharp[ConversionByDatabaseType](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByDatabaseType)]

### <a name="fluent-api"></a>[<span data-ttu-id="16fd0-133">Fluent-API</span><span class="sxs-lookup"><span data-stu-id="16fd0-133">Fluent API</span></span>](#tab/fluent-api)

<!--
                modelBuilder
                    .Entity<Rider2>()
                    .Property(e => e.Mount)
                    .HasColumnType("nvarchar(24)");
-->
[!code-csharp[ConversionByDatabaseTypeFluent](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByDatabaseTypeFluent)]

***

<span data-ttu-id="16fd0-134">Die Enumerationswerte werden dann in der Datenbank ohne weitere Konfiguration in als Zeichen folgen gespeichert <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> .</span><span class="sxs-lookup"><span data-stu-id="16fd0-134">Then the enum values will be saved as strings in the database without any further configuration in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span>

## <a name="the-valueconverter-class"></a><span data-ttu-id="16fd0-135">Die ValueConverter-Klasse</span><span class="sxs-lookup"><span data-stu-id="16fd0-135">The ValueConverter class</span></span>

<span data-ttu-id="16fd0-136"><xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.HasConversion%2A>Wenn Sie wie oben gezeigt aufrufen, wird eine <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.ValueConverter%602> -Instanz erstellt und für die-Eigenschaft festgelegt.</span><span class="sxs-lookup"><span data-stu-id="16fd0-136">Calling <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.HasConversion%2A> as shown above will create a <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.ValueConverter%602> instance and set it on the property.</span></span> <span data-ttu-id="16fd0-137">`ValueConverter`Stattdessen kann explizit erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="16fd0-137">The `ValueConverter` can instead be created explicitly.</span></span> <span data-ttu-id="16fd0-138">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="16fd0-138">For example:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                var converter = new ValueConverter<EquineBeast, string>(
                    v => v.ToString(),
                    v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));

                modelBuilder
                    .Entity<Rider>()
                    .Property(e => e.Mount)
                    .HasConversion(converter);
            }
-->
[!code-csharp[ConversionByConverterInstance](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByConverterInstance)]

<span data-ttu-id="16fd0-139">Dies kann hilfreich sein, wenn mehrere Eigenschaften dieselbe Konvertierung verwenden.</span><span class="sxs-lookup"><span data-stu-id="16fd0-139">This can be useful when multiple properties use the same conversion.</span></span>

## <a name="built-in-converters"></a><span data-ttu-id="16fd0-140">Integrierte Konverter</span><span class="sxs-lookup"><span data-stu-id="16fd0-140">Built-in converters</span></span>

<span data-ttu-id="16fd0-141">Wie bereits erwähnt, wird EF Core mit einem Satz vordefinierter Klassen ausgeliefert <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.ValueConverter%602> , die im- <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion> Namespace enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="16fd0-141">As mentioned above, EF Core ships with a set of pre-defined <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.ValueConverter%602> classes, found in the <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion> namespace.</span></span> <span data-ttu-id="16fd0-142">In vielen Fällen wählt EF den passenden integrierten Konverter basierend auf dem Typ der Eigenschaft im Modell und dem in der Datenbank angeforderten Typ aus, wie oben für enumeraten gezeigt.</span><span class="sxs-lookup"><span data-stu-id="16fd0-142">In many cases EF will choose the appropriate built-in converter based on the type of the property in the model and the type requested in the database, as shown above for enums.</span></span> <span data-ttu-id="16fd0-143">Beispielsweise `.HasConversion<int>()` bewirkt die Verwendung von für eine Eigenschaft, dass `bool` EF Core boolesche Werte in numerische NULL und einen Wert konvertiert:</span><span class="sxs-lookup"><span data-stu-id="16fd0-143">For example, using `.HasConversion<int>()` on a `bool` property will cause EF Core to convert bool values to numerical zero and one values:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                modelBuilder
                    .Entity<User>()
                    .Property(e => e.IsActive)
                    .HasConversion<int>();
            }
-->
[!code-csharp[ConversionByBuiltInBoolToInt](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByBuiltInBoolToInt)]

<span data-ttu-id="16fd0-144">Dies ist funktional identisch mit dem Erstellen einer integrierten Instanz <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToZeroOneConverter%601> und der expliziten Festlegung:</span><span class="sxs-lookup"><span data-stu-id="16fd0-144">This is functionally the same as creating an instance of the built-in <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToZeroOneConverter%601> and setting it explicitly:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                var converter = new BoolToZeroOneConverter<int>();

                modelBuilder
                    .Entity<User>()
                    .Property(e => e.IsActive)
                    .HasConversion(converter);
            }
-->
[!code-csharp[ConversionByBuiltInBoolToIntExplicit](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByBuiltInBoolToIntExplicit)]

<span data-ttu-id="16fd0-145">In der folgenden Tabelle werden häufig verwendete vordefinierte Konvertierungen von Modell-/Eigenschaftentypen in Datenbankanbieter Typen zusammengefasst.</span><span class="sxs-lookup"><span data-stu-id="16fd0-145">The following table summarizes commonly-used pre-defined conversions from model/property types to database provider types.</span></span> <span data-ttu-id="16fd0-146">In der Tabelle `any_numeric_type` ist einer von `int` , `short` , `long` , `byte` , `uint` , `ushort` , `ulong` , `sbyte` , `char` , `decimal` , `float` oder gemeint `double` .</span><span class="sxs-lookup"><span data-stu-id="16fd0-146">In the table `any_numeric_type` means one of `int`, `short`, `long`, `byte`, `uint`, `ushort`, `ulong`, `sbyte`, `char`, `decimal`, `float`, or `double`.</span></span>

| <span data-ttu-id="16fd0-147">Modell-/Eigenschaftentyp</span><span class="sxs-lookup"><span data-stu-id="16fd0-147">Model/property type</span></span> | <span data-ttu-id="16fd0-148">Anbieter/Datenbanktyp</span><span class="sxs-lookup"><span data-stu-id="16fd0-148">Provider/database type</span></span> | <span data-ttu-id="16fd0-149">Konvertierung</span><span class="sxs-lookup"><span data-stu-id="16fd0-149">Conversion</span></span>                                                | <span data-ttu-id="16fd0-150">Verbrauch</span><span class="sxs-lookup"><span data-stu-id="16fd0-150">Usage</span></span>
|:--------------------|------------------------|-----------------------------------------------------------|------
| <span data-ttu-id="16fd0-151">bool</span><span class="sxs-lookup"><span data-stu-id="16fd0-151">bool</span></span>                | <span data-ttu-id="16fd0-152">any_numeric_type</span><span class="sxs-lookup"><span data-stu-id="16fd0-152">any_numeric_type</span></span>       | <span data-ttu-id="16fd0-153">False/true für 0/1</span><span class="sxs-lookup"><span data-stu-id="16fd0-153">False/true to 0/1</span></span>                                         | `.HasConversion<any_numeric_type>()`
|                     | <span data-ttu-id="16fd0-154">any_numeric_type</span><span class="sxs-lookup"><span data-stu-id="16fd0-154">any_numeric_type</span></span>       | <span data-ttu-id="16fd0-155">False/true für beliebige zwei Zahlen</span><span class="sxs-lookup"><span data-stu-id="16fd0-155">False/true to any two numbers</span></span>                             | <span data-ttu-id="16fd0-156">Verwenden Sie <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToTwoValuesConverter%601></span><span class="sxs-lookup"><span data-stu-id="16fd0-156">Use <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToTwoValuesConverter%601></span></span>
|                     | <span data-ttu-id="16fd0-157">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="16fd0-157">string</span></span>                 | <span data-ttu-id="16fd0-158">False/true für "Y"/"N"</span><span class="sxs-lookup"><span data-stu-id="16fd0-158">False/true to "Y"/"N"</span></span>                                     | `.HasConversion<string>()`
|                     | <span data-ttu-id="16fd0-159">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="16fd0-159">string</span></span>                 | <span data-ttu-id="16fd0-160">False/true für beliebige zwei Zeichen folgen</span><span class="sxs-lookup"><span data-stu-id="16fd0-160">False/true to any two strings</span></span>                             | <span data-ttu-id="16fd0-161">Verwenden Sie <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToStringConverter></span><span class="sxs-lookup"><span data-stu-id="16fd0-161">Use <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToStringConverter></span></span>
| <span data-ttu-id="16fd0-162">any_numeric_type</span><span class="sxs-lookup"><span data-stu-id="16fd0-162">any_numeric_type</span></span>    | <span data-ttu-id="16fd0-163">bool</span><span class="sxs-lookup"><span data-stu-id="16fd0-163">bool</span></span>                   | <span data-ttu-id="16fd0-164">0/1 zu false/true</span><span class="sxs-lookup"><span data-stu-id="16fd0-164">0/1 to false/true</span></span>                                         | `.HasConversion<bool>()`
|                     | <span data-ttu-id="16fd0-165">any_numeric_type</span><span class="sxs-lookup"><span data-stu-id="16fd0-165">any_numeric_type</span></span>       | <span data-ttu-id="16fd0-166">Einfache Umwandlung</span><span class="sxs-lookup"><span data-stu-id="16fd0-166">Simple cast</span></span>                                               | `.HasConversion<any_numeric_type>()`
|                     | <span data-ttu-id="16fd0-167">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="16fd0-167">string</span></span>                 | <span data-ttu-id="16fd0-168">Die Zahl als Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="16fd0-168">The number as a string</span></span>                                    | `.HasConversion<string>()`
| <span data-ttu-id="16fd0-169">Enumeration</span><span class="sxs-lookup"><span data-stu-id="16fd0-169">Enum</span></span>                | <span data-ttu-id="16fd0-170">any_numeric_type</span><span class="sxs-lookup"><span data-stu-id="16fd0-170">any_numeric_type</span></span>       | <span data-ttu-id="16fd0-171">Der numerische Wert der Enumeration.</span><span class="sxs-lookup"><span data-stu-id="16fd0-171">The numeric value of the enum</span></span>                             | `.HasConversion<any_numeric_type>()`
|                     | <span data-ttu-id="16fd0-172">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="16fd0-172">string</span></span>                 | <span data-ttu-id="16fd0-173">Die Zeichen folgen Darstellung des Enumerationswerts.</span><span class="sxs-lookup"><span data-stu-id="16fd0-173">The string representation of the enum value</span></span>               | `.HasConversion<string>()`
| <span data-ttu-id="16fd0-174">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="16fd0-174">string</span></span>              | <span data-ttu-id="16fd0-175">bool</span><span class="sxs-lookup"><span data-stu-id="16fd0-175">bool</span></span>                   | <span data-ttu-id="16fd0-176">Analysiert die Zeichenfolge als bool.</span><span class="sxs-lookup"><span data-stu-id="16fd0-176">Parses the string as a bool</span></span>                               | `.HasConversion<bool>()`
|                     | <span data-ttu-id="16fd0-177">any_numeric_type</span><span class="sxs-lookup"><span data-stu-id="16fd0-177">any_numeric_type</span></span>       | <span data-ttu-id="16fd0-178">Analysiert die Zeichenfolge als den angegebenen numerischen Typ.</span><span class="sxs-lookup"><span data-stu-id="16fd0-178">Parses the string as the given numeric type</span></span>               | `.HasConversion<any_numeric_type>()`
|                     | <span data-ttu-id="16fd0-179">char</span><span class="sxs-lookup"><span data-stu-id="16fd0-179">char</span></span>                   | <span data-ttu-id="16fd0-180">Das erste Zeichen der Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="16fd0-180">The first character of the string</span></span>                         | `.HasConversion<char>()`
|                     | <span data-ttu-id="16fd0-181">Datetime</span><span class="sxs-lookup"><span data-stu-id="16fd0-181">DateTime</span></span>               | <span data-ttu-id="16fd0-182">Analysiert die Zeichenfolge als DateTime-Wert.</span><span class="sxs-lookup"><span data-stu-id="16fd0-182">Parses the string as a DateTime</span></span>                           | `.HasConversion<DateTime>()`
|                     | <span data-ttu-id="16fd0-183">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="16fd0-183">DateTimeOffset</span></span>         | <span data-ttu-id="16fd0-184">Analysiert die Zeichenfolge als DateTimeOffset.</span><span class="sxs-lookup"><span data-stu-id="16fd0-184">Parses the string as a DateTimeOffset</span></span>                     | `.HasConversion<DateTimeOffset>()`
|                     | <span data-ttu-id="16fd0-185">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="16fd0-185">TimeSpan</span></span>               | <span data-ttu-id="16fd0-186">Analysiert die Zeichenfolge als TimeSpan.</span><span class="sxs-lookup"><span data-stu-id="16fd0-186">Parses the string as a TimeSpan</span></span>                           | `.HasConversion<TimeSpan>()`
|                     | <span data-ttu-id="16fd0-187">Guid</span><span class="sxs-lookup"><span data-stu-id="16fd0-187">Guid</span></span>                   | <span data-ttu-id="16fd0-188">Analysiert die Zeichenfolge als GUID.</span><span class="sxs-lookup"><span data-stu-id="16fd0-188">Parses the string as a Guid</span></span>                               | `.HasConversion<Guid>()`
|                     | <span data-ttu-id="16fd0-189">byte[]</span><span class="sxs-lookup"><span data-stu-id="16fd0-189">byte[]</span></span>                 | <span data-ttu-id="16fd0-190">Die Zeichenfolge als UTF8-bytes.</span><span class="sxs-lookup"><span data-stu-id="16fd0-190">The string as UTF8 bytes</span></span>                                  | `.HasConversion<byte[]>()`
| <span data-ttu-id="16fd0-191">char</span><span class="sxs-lookup"><span data-stu-id="16fd0-191">char</span></span>                | <span data-ttu-id="16fd0-192">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="16fd0-192">string</span></span>                 | <span data-ttu-id="16fd0-193">Eine Zeichenfolge mit einem einzelnen Zeichen</span><span class="sxs-lookup"><span data-stu-id="16fd0-193">A single character string</span></span>                                 | `.HasConversion<string>()`
| <span data-ttu-id="16fd0-194">Datetime</span><span class="sxs-lookup"><span data-stu-id="16fd0-194">DateTime</span></span>            | <span data-ttu-id="16fd0-195">long</span><span class="sxs-lookup"><span data-stu-id="16fd0-195">long</span></span>                   | <span data-ttu-id="16fd0-196">Codiertes Datum/Uhrzeit-Beibehaltung von DateTime. Kind</span><span class="sxs-lookup"><span data-stu-id="16fd0-196">Encoded date/time preserving DateTime.Kind</span></span>                | `.HasConversion<long>()`
|                     | <span data-ttu-id="16fd0-197">long</span><span class="sxs-lookup"><span data-stu-id="16fd0-197">long</span></span>                   | <span data-ttu-id="16fd0-198">Tickt</span><span class="sxs-lookup"><span data-stu-id="16fd0-198">Ticks</span></span>                                                     | <span data-ttu-id="16fd0-199">Verwenden Sie <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToTicksConverter></span><span class="sxs-lookup"><span data-stu-id="16fd0-199">Use <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToTicksConverter></span></span>
|                     | <span data-ttu-id="16fd0-200">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="16fd0-200">string</span></span>                 | <span data-ttu-id="16fd0-201">Datums-/Uhrzeitzeichenfolge der invarianten Kultur</span><span class="sxs-lookup"><span data-stu-id="16fd0-201">Invariant culture date/time string</span></span>                        | `.HasConversion<string>()`
| <span data-ttu-id="16fd0-202">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="16fd0-202">DateTimeOffset</span></span>      | <span data-ttu-id="16fd0-203">long</span><span class="sxs-lookup"><span data-stu-id="16fd0-203">long</span></span>                   | <span data-ttu-id="16fd0-204">Codiertes Datum/Uhrzeit mit Offset</span><span class="sxs-lookup"><span data-stu-id="16fd0-204">Encoded date/time with offset</span></span>                             | `.HasConversion<long>()`
|                     | <span data-ttu-id="16fd0-205">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="16fd0-205">string</span></span>                 | <span data-ttu-id="16fd0-206">Datum/Uhrzeit-Zeichenfolge der invarianten Kultur mit Offset</span><span class="sxs-lookup"><span data-stu-id="16fd0-206">Invariant culture date/time string with offset</span></span>            | `.HasConversion<string>()`
| <span data-ttu-id="16fd0-207">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="16fd0-207">TimeSpan</span></span>            | <span data-ttu-id="16fd0-208">long</span><span class="sxs-lookup"><span data-stu-id="16fd0-208">long</span></span>                   | <span data-ttu-id="16fd0-209">Tickt</span><span class="sxs-lookup"><span data-stu-id="16fd0-209">Ticks</span></span>                                                     | `.HasConversion<long>()`
|                     | <span data-ttu-id="16fd0-210">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="16fd0-210">string</span></span>                 | <span data-ttu-id="16fd0-211">Zeichenfolge für die invariante Kulturzeit Spanne</span><span class="sxs-lookup"><span data-stu-id="16fd0-211">Invariant culture time span string</span></span>                        | `.HasConversion<string>()`
| <span data-ttu-id="16fd0-212">Uri</span><span class="sxs-lookup"><span data-stu-id="16fd0-212">Uri</span></span>                 | <span data-ttu-id="16fd0-213">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="16fd0-213">string</span></span>                 | <span data-ttu-id="16fd0-214">Der URI als Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="16fd0-214">The URI as a string</span></span>                                       | `.HasConversion<string>()`
| <span data-ttu-id="16fd0-215">PhysicalAddress</span><span class="sxs-lookup"><span data-stu-id="16fd0-215">PhysicalAddress</span></span>     | <span data-ttu-id="16fd0-216">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="16fd0-216">string</span></span>                 | <span data-ttu-id="16fd0-217">Die Adresse als Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="16fd0-217">The address as a string</span></span>                                   | `.HasConversion<string>()`
|                     | <span data-ttu-id="16fd0-218">byte[]</span><span class="sxs-lookup"><span data-stu-id="16fd0-218">byte[]</span></span>                 | <span data-ttu-id="16fd0-219">Bytes in Big-d-Netzwerk Reihenfolge</span><span class="sxs-lookup"><span data-stu-id="16fd0-219">Bytes in big-endian network order</span></span>                         | `.HasConversion<byte[]>()`
| <span data-ttu-id="16fd0-220">IPAddress</span><span class="sxs-lookup"><span data-stu-id="16fd0-220">IPAddress</span></span>           | <span data-ttu-id="16fd0-221">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="16fd0-221">string</span></span>                 | <span data-ttu-id="16fd0-222">Die Adresse als Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="16fd0-222">The address as a string</span></span>                                   | `.HasConversion<string>()`
|                     | <span data-ttu-id="16fd0-223">byte[]</span><span class="sxs-lookup"><span data-stu-id="16fd0-223">byte[]</span></span>                 | <span data-ttu-id="16fd0-224">Bytes in Big-d-Netzwerk Reihenfolge</span><span class="sxs-lookup"><span data-stu-id="16fd0-224">Bytes in big-endian network order</span></span>                         | `.HasConversion<byte[]>()`
| <span data-ttu-id="16fd0-225">Guid</span><span class="sxs-lookup"><span data-stu-id="16fd0-225">Guid</span></span>                | <span data-ttu-id="16fd0-226">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="16fd0-226">string</span></span>                 | <span data-ttu-id="16fd0-227">Der GUID im auf: dddddddd-dddd-dddd-dddd-dddddddddddd-Format.</span><span class="sxs-lookup"><span data-stu-id="16fd0-227">The GUID in 'dddddddd-dddd-dddd-dddd-dddddddddddd' format</span></span> | `.HasConversion<string>()`
|                     | <span data-ttu-id="16fd0-228">byte[]</span><span class="sxs-lookup"><span data-stu-id="16fd0-228">byte[]</span></span>                 | <span data-ttu-id="16fd0-229">Bytes in der binären Serialisierungsreihenfolge von .net</span><span class="sxs-lookup"><span data-stu-id="16fd0-229">Bytes in .NET binary serialization order</span></span>                  | `.HasConversion<byte[]>()`

<span data-ttu-id="16fd0-230">Beachten Sie, dass diese Konvertierungen davon ausgehen, dass das Format des Werts für die Konvertierung geeignet ist.</span><span class="sxs-lookup"><span data-stu-id="16fd0-230">Note that these conversions assume that the format of the value is appropriate for the conversion.</span></span> <span data-ttu-id="16fd0-231">Wenn z. b. Zeichen folgen in Zahlen konvertiert werden, treten Fehler auf, wenn die Zeichen folgen Werte nicht als Zahlen analysiert werden können.</span><span class="sxs-lookup"><span data-stu-id="16fd0-231">For example, converting strings to numbers will fail if the string values cannot be parsed as numbers.</span></span>

<span data-ttu-id="16fd0-232">Die vollständige Liste der integrierten Konverter lautet:</span><span class="sxs-lookup"><span data-stu-id="16fd0-232">The full list of built-in converters is:</span></span>

* <span data-ttu-id="16fd0-233">Umstellen von booleschen Eigenschaften:</span><span class="sxs-lookup"><span data-stu-id="16fd0-233">Converting bool properties:</span></span>
  * <span data-ttu-id="16fd0-234"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToStringConverter> -Bool in Zeichen folgen wie "Y" und "N"</span><span class="sxs-lookup"><span data-stu-id="16fd0-234"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToStringConverter> - Bool to strings such as "Y" and "N"</span></span>
  * <span data-ttu-id="16fd0-235"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToTwoValuesConverter%601> -Bool auf zwei beliebige Werte</span><span class="sxs-lookup"><span data-stu-id="16fd0-235"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToTwoValuesConverter%601> - Bool to any two values</span></span>
  * <span data-ttu-id="16fd0-236"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToZeroOneConverter%601> -Bool in NULL und eins</span><span class="sxs-lookup"><span data-stu-id="16fd0-236"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToZeroOneConverter%601> - Bool to zero and one</span></span>
* <span data-ttu-id="16fd0-237">Bytearray-Eigenschaften werden umgerechnet:</span><span class="sxs-lookup"><span data-stu-id="16fd0-237">Converting byte array properties:</span></span>
  * <span data-ttu-id="16fd0-238"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BytesToStringConverter> -Bytearray in Base64-codierte Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="16fd0-238"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BytesToStringConverter> - Byte array to Base64-encoded string</span></span>
* <span data-ttu-id="16fd0-239">Jede Konvertierung, die nur eine Typumwandlung erfordert</span><span class="sxs-lookup"><span data-stu-id="16fd0-239">Any conversion that requires only a type-cast</span></span>
  * <span data-ttu-id="16fd0-240"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.CastingConverter%602> -Konvertierungen, die nur eine Typumwandlung erfordern</span><span class="sxs-lookup"><span data-stu-id="16fd0-240"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.CastingConverter%602> - Conversions that require only a type cast</span></span>
* <span data-ttu-id="16fd0-241">Char-Eigenschaften werden umgerechnet:</span><span class="sxs-lookup"><span data-stu-id="16fd0-241">Converting char properties:</span></span>
  * <span data-ttu-id="16fd0-242"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.CharToStringConverter> -Char in eine Zeichenfolge mit einem Zeichen</span><span class="sxs-lookup"><span data-stu-id="16fd0-242"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.CharToStringConverter> - Char to single character string</span></span>
* <span data-ttu-id="16fd0-243">Eigenschaften werden umgerechnet <xref:System.DateTimeOffset> :</span><span class="sxs-lookup"><span data-stu-id="16fd0-243">Converting <xref:System.DateTimeOffset> properties:</span></span>
  * <span data-ttu-id="16fd0-244"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeOffsetToBinaryConverter> - <xref:System.DateTimeOffset> in binär codierten 64-Bit-Wert</span><span class="sxs-lookup"><span data-stu-id="16fd0-244"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeOffsetToBinaryConverter> - <xref:System.DateTimeOffset> to binary-encoded 64-bit value</span></span>
  * <span data-ttu-id="16fd0-245"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeOffsetToBytesConverter> - <xref:System.DateTimeOffset> in Bytearray</span><span class="sxs-lookup"><span data-stu-id="16fd0-245"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeOffsetToBytesConverter> - <xref:System.DateTimeOffset> to byte array</span></span>
  * <span data-ttu-id="16fd0-246"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeOffsetToStringConverter> - <xref:System.DateTimeOffset> in Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="16fd0-246"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeOffsetToStringConverter> - <xref:System.DateTimeOffset> to string</span></span>
* <span data-ttu-id="16fd0-247">Eigenschaften werden umgerechnet <xref:System.DateTime> :</span><span class="sxs-lookup"><span data-stu-id="16fd0-247">Converting <xref:System.DateTime> properties:</span></span>
  * <span data-ttu-id="16fd0-248"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToBinaryConverter> - <xref:System.DateTime> auf 64-Bit-Wert einschließlich DateTimeKind</span><span class="sxs-lookup"><span data-stu-id="16fd0-248"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToBinaryConverter> - <xref:System.DateTime> to 64-bit value including DateTimeKind</span></span>
  * <span data-ttu-id="16fd0-249"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToStringConverter> - <xref:System.DateTime> in Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="16fd0-249"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToStringConverter> - <xref:System.DateTime> to string</span></span>
  * <span data-ttu-id="16fd0-250"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToTicksConverter> - <xref:System.DateTime> in Ticks</span><span class="sxs-lookup"><span data-stu-id="16fd0-250"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToTicksConverter> - <xref:System.DateTime> to ticks</span></span>
* <span data-ttu-id="16fd0-251">Eigenschaften von Enumeration werden umgerechnet:</span><span class="sxs-lookup"><span data-stu-id="16fd0-251">Converting enum properties:</span></span>
  * <span data-ttu-id="16fd0-252"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.EnumToNumberConverter%602> -Enumeration zur zugrunde liegenden Zahl</span><span class="sxs-lookup"><span data-stu-id="16fd0-252"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.EnumToNumberConverter%602> - Enum to underlying number</span></span>
  * <span data-ttu-id="16fd0-253"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.EnumToStringConverter%601> -Enum in Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="16fd0-253"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.EnumToStringConverter%601> - Enum to string</span></span>
* <span data-ttu-id="16fd0-254">Eigenschaften werden umgerechnet <xref:System.Guid> :</span><span class="sxs-lookup"><span data-stu-id="16fd0-254">Converting <xref:System.Guid> properties:</span></span>
  * <span data-ttu-id="16fd0-255"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.GuidToBytesConverter> - <xref:System.Guid> in Bytearray</span><span class="sxs-lookup"><span data-stu-id="16fd0-255"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.GuidToBytesConverter> - <xref:System.Guid> to byte array</span></span>
  * <span data-ttu-id="16fd0-256"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.GuidToStringConverter> - <xref:System.Guid> in Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="16fd0-256"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.GuidToStringConverter> - <xref:System.Guid> to string</span></span>
* <span data-ttu-id="16fd0-257">Eigenschaften werden umgerechnet <xref:System.Net.IPAddress> :</span><span class="sxs-lookup"><span data-stu-id="16fd0-257">Converting <xref:System.Net.IPAddress> properties:</span></span>
  * <span data-ttu-id="16fd0-258"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.IPAddressToBytesConverter> - <xref:System.Net.IPAddress> in Bytearray</span><span class="sxs-lookup"><span data-stu-id="16fd0-258"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.IPAddressToBytesConverter> - <xref:System.Net.IPAddress> to byte array</span></span>
  * <span data-ttu-id="16fd0-259"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.IPAddressToStringConverter> - <xref:System.Net.IPAddress> in Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="16fd0-259"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.IPAddressToStringConverter> - <xref:System.Net.IPAddress> to string</span></span>
* <span data-ttu-id="16fd0-260">Umrechnen von numerischen Eigenschaften (int, Double, Decimal usw.):</span><span class="sxs-lookup"><span data-stu-id="16fd0-260">Converting numeric (int, double, decimal, etc.) properties:</span></span>
  * <span data-ttu-id="16fd0-261"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.NumberToBytesConverter%601> -Beliebiger numerischer Wert zum Bytearray</span><span class="sxs-lookup"><span data-stu-id="16fd0-261"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.NumberToBytesConverter%601> - Any numerical value to byte array</span></span>
  * <span data-ttu-id="16fd0-262"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.NumberToStringConverter%601> -Beliebiger numerischer Wert zu Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="16fd0-262"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.NumberToStringConverter%601> - Any numerical value to string</span></span>
* <span data-ttu-id="16fd0-263">Eigenschaften werden umgerechnet <xref:System.Net.NetworkInformation.PhysicalAddress> :</span><span class="sxs-lookup"><span data-stu-id="16fd0-263">Converting <xref:System.Net.NetworkInformation.PhysicalAddress> properties:</span></span>
  * <span data-ttu-id="16fd0-264"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.PhysicalAddressToBytesConverter> - <xref:System.Net.NetworkInformation.PhysicalAddress> in Bytearray</span><span class="sxs-lookup"><span data-stu-id="16fd0-264"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.PhysicalAddressToBytesConverter> - <xref:System.Net.NetworkInformation.PhysicalAddress> to byte array</span></span>
  * <span data-ttu-id="16fd0-265"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.PhysicalAddressToStringConverter> - <xref:System.Net.NetworkInformation.PhysicalAddress> in Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="16fd0-265"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.PhysicalAddressToStringConverter> - <xref:System.Net.NetworkInformation.PhysicalAddress> to string</span></span>
* <span data-ttu-id="16fd0-266">Zeichen folgen Eigenschaften werden umgerechnet:</span><span class="sxs-lookup"><span data-stu-id="16fd0-266">Converting string properties:</span></span>
  * <span data-ttu-id="16fd0-267"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToBoolConverter> -Zeichen folgen wie "Y" und "N" in "bool"</span><span class="sxs-lookup"><span data-stu-id="16fd0-267"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToBoolConverter> - Strings such as "Y" and "N" to bool</span></span>
  * <span data-ttu-id="16fd0-268"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToBytesConverter> -Zeichenfolge in UTF8-bytes</span><span class="sxs-lookup"><span data-stu-id="16fd0-268"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToBytesConverter> - String to UTF8 bytes</span></span>
  * <span data-ttu-id="16fd0-269"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToCharConverter> -Zeichenfolge in Zeichen</span><span class="sxs-lookup"><span data-stu-id="16fd0-269"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToCharConverter> - String to character</span></span>
  * <span data-ttu-id="16fd0-270"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToDateTimeConverter> -Zeichenfolge in <xref:System.DateTime></span><span class="sxs-lookup"><span data-stu-id="16fd0-270"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToDateTimeConverter> - String to <xref:System.DateTime></span></span>
  * <span data-ttu-id="16fd0-271"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToDateTimeOffsetConverter> -Zeichenfolge in <xref:System.DateTimeOffset></span><span class="sxs-lookup"><span data-stu-id="16fd0-271"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToDateTimeOffsetConverter> - String to <xref:System.DateTimeOffset></span></span>
  * <span data-ttu-id="16fd0-272"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToEnumConverter%601> -Zeichenfolge in Enumeration</span><span class="sxs-lookup"><span data-stu-id="16fd0-272"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToEnumConverter%601> - String to enum</span></span>
  * <span data-ttu-id="16fd0-273"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToGuidConverter> -Zeichenfolge in <xref:System.Guid></span><span class="sxs-lookup"><span data-stu-id="16fd0-273"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToGuidConverter> - String to <xref:System.Guid></span></span>
  * <span data-ttu-id="16fd0-274"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToNumberConverter%601> -Zeichenfolge in numerischen Typ</span><span class="sxs-lookup"><span data-stu-id="16fd0-274"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToNumberConverter%601> - String to numeric type</span></span>
  * <span data-ttu-id="16fd0-275"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToTimeSpanConverter> -Zeichenfolge in <xref:System.TimeSpan></span><span class="sxs-lookup"><span data-stu-id="16fd0-275"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToTimeSpanConverter> - String to <xref:System.TimeSpan></span></span>
  * <span data-ttu-id="16fd0-276"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToUriConverter> -Zeichenfolge in <xref:System.Uri></span><span class="sxs-lookup"><span data-stu-id="16fd0-276"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToUriConverter> - String to <xref:System.Uri></span></span>
* <span data-ttu-id="16fd0-277">Eigenschaften werden umgerechnet <xref:System.TimeSpan> :</span><span class="sxs-lookup"><span data-stu-id="16fd0-277">Converting <xref:System.TimeSpan> properties:</span></span>
  * <span data-ttu-id="16fd0-278"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.TimeSpanToStringConverter> - <xref:System.TimeSpan> in Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="16fd0-278"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.TimeSpanToStringConverter> - <xref:System.TimeSpan> to string</span></span>
  * <span data-ttu-id="16fd0-279"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.TimeSpanToTicksConverter> - <xref:System.TimeSpan> in Ticks</span><span class="sxs-lookup"><span data-stu-id="16fd0-279"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.TimeSpanToTicksConverter> - <xref:System.TimeSpan> to ticks</span></span>
* <span data-ttu-id="16fd0-280">Eigenschaften werden umgerechnet <xref:System.Uri> :</span><span class="sxs-lookup"><span data-stu-id="16fd0-280">Converting <xref:System.Uri> properties:</span></span>
  * <span data-ttu-id="16fd0-281"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.UriToStringConverter> - <xref:System.Uri> in Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="16fd0-281"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.UriToStringConverter> - <xref:System.Uri> to string</span></span>

<span data-ttu-id="16fd0-282">Beachten Sie, dass alle integrierten Konverter zustandslos sind, sodass eine einzelne Instanz auf sichere Weise von mehreren Eigenschaften gemeinsam genutzt werden kann.</span><span class="sxs-lookup"><span data-stu-id="16fd0-282">Note that all the built-in converters are stateless and so a single instance can be safely shared by multiple properties.</span></span>

## <a name="column-facets-and-mapping-hints"></a><span data-ttu-id="16fd0-283">Spalten Facetten und Mapping-Hinweise</span><span class="sxs-lookup"><span data-stu-id="16fd0-283">Column facets and mapping hints</span></span>

<span data-ttu-id="16fd0-284">Einige Datenbanktypen verfügen über Facetten, die die Speicherung der Daten ändern.</span><span class="sxs-lookup"><span data-stu-id="16fd0-284">Some database types have facets that modify how the data is stored.</span></span> <span data-ttu-id="16fd0-285">Dazu gehören:</span><span class="sxs-lookup"><span data-stu-id="16fd0-285">These include:</span></span>

* <span data-ttu-id="16fd0-286">Genauigkeit und Dezimalstellen für Dezimalzahlen und Datums-/Uhrzeitspalten</span><span class="sxs-lookup"><span data-stu-id="16fd0-286">Precision and scale for decimals and date/time columns</span></span>
* <span data-ttu-id="16fd0-287">Größe/Länge für binäre Spalten und Zeichen folgen Spalten</span><span class="sxs-lookup"><span data-stu-id="16fd0-287">Size/length for binary and string columns</span></span>
* <span data-ttu-id="16fd0-288">Unicode für Zeichen folgen Spalten</span><span class="sxs-lookup"><span data-stu-id="16fd0-288">Unicode for string columns</span></span>

<span data-ttu-id="16fd0-289">Diese Facetten können für eine Eigenschaft, die einen Wert Konverter verwendet, auf normale Weise konfiguriert werden und gelten für den konvertierten Datenbanktyp.</span><span class="sxs-lookup"><span data-stu-id="16fd0-289">These facets can be configured in the normal way for a property that uses a value converter, and will apply to the converted database type.</span></span> <span data-ttu-id="16fd0-290">Wenn Sie z. b. von einer Enumeration in Zeichen folgen umrechnen, können wir angeben, dass die Daten Bank Spalte nicht-Unicode sein soll und bis zu 20 Zeichen speichern soll:</span><span class="sxs-lookup"><span data-stu-id="16fd0-290">For example, when converting from an enum to strings, we can specify that the database column should be non-Unicode and store up to 20 characters:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                modelBuilder
                    .Entity<Rider>()
                    .Property(e => e.Mount)
                    .HasConversion<string>()
                    .HasMaxLength(20)
                    .IsUnicode(false);
            }
-->
[!code-csharp[ConversionByClrTypeWithFacets](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByClrTypeWithFacets)]

<span data-ttu-id="16fd0-291">Oder, wenn der Konverter explizit erstellt wird:</span><span class="sxs-lookup"><span data-stu-id="16fd0-291">Or, when creating the converter explicitly:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                var converter = new ValueConverter<EquineBeast, string>(
                    v => v.ToString(),
                    v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));

                modelBuilder
                    .Entity<Rider>()
                    .Property(e => e.Mount)
                    .HasConversion(converter)
                    .HasMaxLength(20)
                    .IsUnicode(false);
            }
-->
[!code-csharp[ConversionByConverterInstanceWithFacets](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByConverterInstanceWithFacets)]

<span data-ttu-id="16fd0-292">Dies führt zu einer `varchar(20)` Spalte, wenn EF Core Migrationen für SQL Server verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="16fd0-292">This results in a `varchar(20)` column when using EF Core migrations against SQL Server:</span></span>

```sql
CREATE TABLE [Rider] (
    [Id] int NOT NULL IDENTITY,
    [Mount] varchar(20) NOT NULL,
    CONSTRAINT [PK_Rider] PRIMARY KEY ([Id]));
```

<span data-ttu-id="16fd0-293">Wenn allerdings standardmäßig alle `EquineBeast` Spalten lauten sollen `varchar(20)` , können diese Informationen dem Wert Konverter als übergeben werden <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.ConverterMappingHints> .</span><span class="sxs-lookup"><span data-stu-id="16fd0-293">However, if by default all `EquineBeast` columns should be `varchar(20)`, then this information can be given to the value converter as a <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.ConverterMappingHints>.</span></span> <span data-ttu-id="16fd0-294">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="16fd0-294">For example:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                var converter = new ValueConverter<EquineBeast, string>(
                    v => v.ToString(),
                    v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v),
                    new ConverterMappingHints(size: 20, unicode: false));

                modelBuilder
                    .Entity<Rider>()
                    .Property(e => e.Mount)
                    .HasConversion(converter);
            }
-->
[!code-csharp[ConversionByConverterInstanceWithMappingHints](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByConverterInstanceWithMappingHints)]

<span data-ttu-id="16fd0-295">Bei jeder Verwendung dieses Konverters ist die Daten Bank Spalte nicht-Unicode mit einer maximalen Länge von 20.</span><span class="sxs-lookup"><span data-stu-id="16fd0-295">Now any time this converter is used, the database column will be non-unicode with a max length of 20.</span></span> <span data-ttu-id="16fd0-296">Dies sind jedoch nur Hinweise, da Sie von allen Facetten überschrieben werden, die explizit für die zugeordnete Eigenschaft festgelegt wurden.</span><span class="sxs-lookup"><span data-stu-id="16fd0-296">However, these are only hints since they are be overridden by any facets explicitly set on the mapped property.</span></span>

## <a name="examples"></a><span data-ttu-id="16fd0-297">Beispiele</span><span class="sxs-lookup"><span data-stu-id="16fd0-297">Examples</span></span>

### <a name="simple-value-objects"></a><span data-ttu-id="16fd0-298">Einfache Wert Objekte</span><span class="sxs-lookup"><span data-stu-id="16fd0-298">Simple value objects</span></span>

<span data-ttu-id="16fd0-299">In diesem Beispiel wird ein einfacher Typ verwendet, um einen primitiven Typ zu wrappen.</span><span class="sxs-lookup"><span data-stu-id="16fd0-299">This example uses a simple type to wrap a primitive type.</span></span> <span data-ttu-id="16fd0-300">Dies kann hilfreich sein, wenn Sie möchten, dass der Typ im Modell spezifischer (und somit typsicherer) als ein primitiver Typ ist.</span><span class="sxs-lookup"><span data-stu-id="16fd0-300">This can be useful when you want the type in your model to be more specific (and hence more type-safe) than a primitive type.</span></span> <span data-ttu-id="16fd0-301">In diesem Beispiel ist `Dollars` der Typ, der den dezimalen primitiven umschließt:</span><span class="sxs-lookup"><span data-stu-id="16fd0-301">In this example, that type is `Dollars`, which wraps the decimal primitive:</span></span>

<!--
        public readonly struct Dollars
        {
            public Dollars(decimal amount) 
                => Amount = amount;
            
            public decimal Amount { get; }

            public override string ToString() 
                => $"${Amount}";
        }
-->
[!code-csharp[SimpleValueObject](../../../samples/core/Modeling/ValueConversions/SimpleValueObject.cs?name=SimpleValueObject)]

<span data-ttu-id="16fd0-302">Dies kann in einem Entitätstyp verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="16fd0-302">This can be used in an entity type:</span></span>

<!--
        public class Order
        {
            public int Id { get; set; }

            public Dollars Price { get; set; }
        }
-->
[!code-csharp[SimpleValueObjectModel](../../../samples/core/Modeling/ValueConversions/SimpleValueObject.cs?name=SimpleValueObjectModel)]

<span data-ttu-id="16fd0-303">Und in die zugrunde liegende konvertiert, `decimal` Wenn Sie in der Datenbank gespeichert werden:</span><span class="sxs-lookup"><span data-stu-id="16fd0-303">And converted to the underlying `decimal` when stored in the database:</span></span>

<!--
                modelBuilder.Entity<Order>()
                    .Property(e => e.Price)
                    .HasConversion(
                        v => v.Amount,
                        v => new Dollars(v));
-->
[!code-csharp[ConfigureImmutableStructProperty](../../../samples/core/Modeling/ValueConversions/SimpleValueObject.cs?name=ConfigureImmutableStructProperty)]

> [!NOTE]
> <span data-ttu-id="16fd0-304">Dieses Wertobjekt wird als schreibgeschützte [Struktur](/dotnet/csharp/language-reference/builtin-types/struct)implementiert.</span><span class="sxs-lookup"><span data-stu-id="16fd0-304">This value object is implemented as a [readonly struct](/dotnet/csharp/language-reference/builtin-types/struct).</span></span> <span data-ttu-id="16fd0-305">Dies bedeutet, dass EF Core Werte ohne Probleme erstellen und vergleichen können.</span><span class="sxs-lookup"><span data-stu-id="16fd0-305">This means that EF Core can snapshot and compare values without issue.</span></span> <span data-ttu-id="16fd0-306">Weitere Informationen finden Sie unter [Wert Vergleiche](xref:core/modeling/value-comparers) .</span><span class="sxs-lookup"><span data-stu-id="16fd0-306">See [Value Comparers](xref:core/modeling/value-comparers) for more information.</span></span>

### <a name="composite-value-objects"></a><span data-ttu-id="16fd0-307">Zusammengesetzte Wert Objekte</span><span class="sxs-lookup"><span data-stu-id="16fd0-307">Composite value objects</span></span>

<span data-ttu-id="16fd0-308">Im vorherigen Beispiel enthielt der Wert Objekttyp nur eine einzelne Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="16fd0-308">In the previous example, the value object type contained only a single property.</span></span> <span data-ttu-id="16fd0-309">Es kommt häufiger vor, dass ein Wert Objekttyp mehrere Eigenschaften zusammenfasst, die zusammen ein Domänen Konzept bilden.</span><span class="sxs-lookup"><span data-stu-id="16fd0-309">It is more common for a value object type to compose multiple properties that together form a domain concept.</span></span> <span data-ttu-id="16fd0-310">Beispielsweise ein allgemeiner `Money` Typ, der sowohl den Betrag als auch die Währung enthält:</span><span class="sxs-lookup"><span data-stu-id="16fd0-310">For example, a general `Money` type that contains both the amount and the currency:</span></span>

<!--
        public readonly struct Money
        {
            [JsonConstructor]
            public Money(decimal amount, Currency currency)
            {
                Amount = amount;
                Currency = currency;
            }

            public override string ToString()
                => (Currency == Currency.UsDollars ? "$" : "£") + Amount;

            public decimal Amount { get; }
            public Currency Currency { get; }
        }

        public enum Currency
        {
            UsDollars,
            PoundsStirling
        }
-->
[!code-csharp[CompositeValueObject](../../../samples/core/Modeling/ValueConversions/CompositeValueObject.cs?name=CompositeValueObject)]

<span data-ttu-id="16fd0-311">Dieses Wertobjekt kann in einem Entitätstyp wie zuvor verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="16fd0-311">This value object can be used in an entity type as before:</span></span>

<!--
        public class Order
        {
            public int Id { get; set; }

            public Money Price { get; set; }
        }
-->
[!code-csharp[CompositeValueObjectModel](../../../samples/core/Modeling/ValueConversions/CompositeValueObject.cs?name=CompositeValueObjectModel)]

<span data-ttu-id="16fd0-312">Wert Konverter können derzeit nur Werte in eine und aus einer einzelnen Daten Bank Spalte konvertieren.</span><span class="sxs-lookup"><span data-stu-id="16fd0-312">Value converters can currently only convert values to and from a single database column.</span></span> <span data-ttu-id="16fd0-313">Diese Einschränkung bedeutet, dass alle Eigenschaftswerte aus dem Objekt in einen einzelnen Spaltenwert codiert werden müssen.</span><span class="sxs-lookup"><span data-stu-id="16fd0-313">This limitation means that all property values from the object must be encoded into a single column value.</span></span> <span data-ttu-id="16fd0-314">Dies wird in der Regel durch Serialisieren des Objekts in die Datenbank und anschließendes Deserialisieren des Objekts behandelt. Verwenden Sie beispielsweise Folgendes <xref:System.Text.Json> :</span><span class="sxs-lookup"><span data-stu-id="16fd0-314">This is typically handled by serializing the object as it goes into the database, and then deserializing it again on the way out. For example, using <xref:System.Text.Json>:</span></span>

<!--
                modelBuilder.Entity<Order>()
                    .Property(e => e.Price)
                    .HasConversion(
                        v => JsonSerializer.Serialize(v, null),
                        v => JsonSerializer.Deserialize<Money>(v, null));
-->
[!code-csharp[ConfigureCompositeValueObject](../../../samples/core/Modeling/ValueConversions/CompositeValueObject.cs?name=ConfigureCompositeValueObject)]

> [!NOTE]
> <span data-ttu-id="16fd0-315">Wir planen, das Mapping eines Objekts mehreren Spalten in EF Core 6,0 zu ermöglichen. Hierdurch entfällt die Notwendigkeit, die Serialisierung zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="16fd0-315">We plan to allow mapping an object to multiple columns in EF Core 6.0, removing the need to use serialization here.</span></span> <span data-ttu-id="16fd0-316">Dies wird durch das [GitHub-Problem #13947](https://github.com/dotnet/efcore/issues/13947)nachverfolgt.</span><span class="sxs-lookup"><span data-stu-id="16fd0-316">This is tracked by [GitHub issue #13947](https://github.com/dotnet/efcore/issues/13947).</span></span>

> [!NOTE]
> <span data-ttu-id="16fd0-317">Wie im vorherigen Beispiel wird dieses Wertobjekt als schreibgeschützte [Struktur](/dotnet/csharp/language-reference/builtin-types/struct)implementiert.</span><span class="sxs-lookup"><span data-stu-id="16fd0-317">As with the previous example, this value object is implemented as a [readonly struct](/dotnet/csharp/language-reference/builtin-types/struct).</span></span> <span data-ttu-id="16fd0-318">Dies bedeutet, dass EF Core Werte ohne Probleme erstellen und vergleichen können.</span><span class="sxs-lookup"><span data-stu-id="16fd0-318">This means that EF Core can snapshot and compare values without issue.</span></span> <span data-ttu-id="16fd0-319">Weitere Informationen finden Sie unter [Wert Vergleiche](xref:core/modeling/value-comparers) .</span><span class="sxs-lookup"><span data-stu-id="16fd0-319">See [Value Comparers](xref:core/modeling/value-comparers) for more information.</span></span>

### <a name="collections-of-primitives"></a><span data-ttu-id="16fd0-320">Auflistungen von primitiven</span><span class="sxs-lookup"><span data-stu-id="16fd0-320">Collections of primitives</span></span>

<span data-ttu-id="16fd0-321">Die Serialisierung kann auch zum Speichern einer Auflistung primitiver Werte verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="16fd0-321">Serialization can also be used to store a collection of primitive values.</span></span> <span data-ttu-id="16fd0-322">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="16fd0-322">For example:</span></span>

<!--
        public class Post
        {
            public int Id { get; set; }
            public string Title { get; set; }
            public string Contents { get; set; }

            public ICollection<string> Tags { get; set; }
        }
-->
[!code-csharp[PrimitiveCollectionModel](../../../samples/core/Modeling/ValueConversions/PrimitiveCollection.cs?name=PrimitiveCollectionModel)]

<span data-ttu-id="16fd0-323">Erneutes verwenden <xref:System.Text.Json> :</span><span class="sxs-lookup"><span data-stu-id="16fd0-323">Using <xref:System.Text.Json> again:</span></span>

<!--
                modelBuilder.Entity<Post>()
                    .Property(e => e.Tags)
                    .HasConversion(
                        v => JsonSerializer.Serialize(v, null),
                        v => JsonSerializer.Deserialize<List<string>>(v, null),
                        new ValueComparer<ICollection<string>>(
                            (c1, c2) => c1.SequenceEqual(c2),
                            c => c.Aggregate(0, (a, v) => HashCode.Combine(a, v.GetHashCode())),
                            c => (ICollection<string>)c.ToList()));
-->
[!code-csharp[ConfigurePrimitiveCollection](../../../samples/core/Modeling/ValueConversions/PrimitiveCollection.cs?name=ConfigurePrimitiveCollection)]

<span data-ttu-id="16fd0-324">`ICollection<string>` stellt einen änderbaren Referenztyp dar.</span><span class="sxs-lookup"><span data-stu-id="16fd0-324">`ICollection<string>` represents a mutable reference type.</span></span> <span data-ttu-id="16fd0-325">Dies bedeutet, dass eine <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> erforderlich ist, damit EF Core Änderungen ordnungsgemäß verfolgen und erkennen können.</span><span class="sxs-lookup"><span data-stu-id="16fd0-325">This means that a <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> is needed so that EF Core can track and detect changes correctly.</span></span> <span data-ttu-id="16fd0-326">Weitere Informationen finden Sie unter [Wert Vergleiche](xref:core/modeling/value-comparers) .</span><span class="sxs-lookup"><span data-stu-id="16fd0-326">See [Value Comparers](xref:core/modeling/value-comparers) for more information.</span></span>

### <a name="collections-of-value-objects"></a><span data-ttu-id="16fd0-327">Auflistungen von Wert Objekten</span><span class="sxs-lookup"><span data-stu-id="16fd0-327">Collections of value objects</span></span>

<span data-ttu-id="16fd0-328">Wenn Sie die beiden vorherigen Beispiele zusammen kombinieren, können wir eine Auflistung von Wert Objekten erstellen.</span><span class="sxs-lookup"><span data-stu-id="16fd0-328">Combining the previous two examples together we can create a collection of value objects.</span></span> <span data-ttu-id="16fd0-329">Stellen Sie sich beispielsweise einen Typ vor, `AnnualFinance` der die Blog Finanzen für ein einzelnes Jahr modelliert:</span><span class="sxs-lookup"><span data-stu-id="16fd0-329">For example, consider an `AnnualFinance` type that models blog finances for a single year:</span></span>

<!--
        public readonly struct AnnualFinance
        {
            [JsonConstructor]
            public AnnualFinance(int year, Money income, Money expenses)
            {
                Year = year;
                Income = income;
                Expenses = expenses;
            }

            public int Year { get; }
            public Money Income { get; }
            public Money Expenses { get; }
            public Money Revenue => new Money(Income.Amount - Expenses.Amount, Income.Currency);
        }
-->
[!code-csharp[ValueObjectCollection](../../../samples/core/Modeling/ValueConversions/ValueObjectCollection.cs?name=ValueObjectCollection)]

<span data-ttu-id="16fd0-330">Dieser Typ verfasst einige der Typen, die `Money` wir zuvor erstellt haben:</span><span class="sxs-lookup"><span data-stu-id="16fd0-330">This type composes several of the `Money` types we created previously:</span></span>

<!--
        public readonly struct Money
        {
            [JsonConstructor]
            public Money(decimal amount, Currency currency)
            {
                Amount = amount;
                Currency = currency;
            }

            public override string ToString()
                => (Currency == Currency.UsDollars ? "$" : "£") + Amount;

            public decimal Amount { get; }
            public Currency Currency { get; }
        }

        public enum Currency
        {
            UsDollars,
            PoundsStirling
        }
-->
[!code-csharp[ValueObjectCollectionMoney](../../../samples/core/Modeling/ValueConversions/ValueObjectCollection.cs?name=ValueObjectCollectionMoney)]

<span data-ttu-id="16fd0-331">Anschließend können wir dem Entitätstyp eine Auflistung von hinzufügen `AnnualFinance` :</span><span class="sxs-lookup"><span data-stu-id="16fd0-331">We can then add a collection of `AnnualFinance` to our entity type:</span></span>

<!--
        public class Blog
        {
            public int Id { get; set; }
            public string Name { get; set; }
            
            public IList<AnnualFinance> Finances { get; set; }
        }
-->
[!code-csharp[ValueObjectCollectionModel](../../../samples/core/Modeling/ValueConversions/ValueObjectCollection.cs?name=ValueObjectCollectionModel)]

<span data-ttu-id="16fd0-332">Verwenden Sie die Serialisierung auch, um Folgendes zu speichern:</span><span class="sxs-lookup"><span data-stu-id="16fd0-332">And again use serialization to store this:</span></span>

<!--
                modelBuilder.Entity<Blog>()
                    .Property(e => e.Finances)
                    .HasConversion(
                        v => JsonSerializer.Serialize(v, null),
                        v => JsonSerializer.Deserialize<List<AnnualFinance>>(v, null),
                        new ValueComparer<IList<AnnualFinance>>(
                            (c1, c2) => c1.SequenceEqual(c2),
                            c => c.Aggregate(0, (a, v) => HashCode.Combine(a, v.GetHashCode())),
                            c => (IList<AnnualFinance>)c.ToList()));
-->
[!code-csharp[ConfigureValueObjectCollection](../../../samples/core/Modeling/ValueConversions/ValueObjectCollection.cs?name=ConfigureValueObjectCollection)]

> [!NOTE]
> <span data-ttu-id="16fd0-333">Wie zuvor ist für diese Konvertierung eine erforderlich <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> .</span><span class="sxs-lookup"><span data-stu-id="16fd0-333">As before, this conversion requires a <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601>.</span></span> <span data-ttu-id="16fd0-334">Weitere Informationen finden Sie unter [Wert Vergleiche](xref:core/modeling/value-comparers) .</span><span class="sxs-lookup"><span data-stu-id="16fd0-334">See [Value Comparers](xref:core/modeling/value-comparers) for more information.</span></span>

### <a name="value-objects-as-keys"></a><span data-ttu-id="16fd0-335">Wert Objekte als Schlüssel</span><span class="sxs-lookup"><span data-stu-id="16fd0-335">Value objects as keys</span></span>

<span data-ttu-id="16fd0-336">Manchmal können primitive Schlüsseleigenschaften in Wert Objekte umschließt werden, um beim Zuweisen von Werten eine zusätzliche Ebene der Typsicherheit hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="16fd0-336">Sometimes primitive key properties may be wrapped in value objects to add an additional level of type-safety in assigning values.</span></span> <span data-ttu-id="16fd0-337">Wir könnten beispielsweise einen Schlüsseltyp für Blogs und einen Schlüsseltyp für Beiträge implementieren:</span><span class="sxs-lookup"><span data-stu-id="16fd0-337">For example, we could implement a key type for blogs, and a key type for posts:</span></span>

<!--
        public readonly struct BlogKey
        {
            public BlogKey(int id) => Id = id;
            public int Id { get; }
        }

        public readonly struct PostKey
        {
            public PostKey(int id) => Id = id;
            public int Id { get; }
        }
-->
[!code-csharp[KeyValueObjects](../../../samples/core/Modeling/ValueConversions/KeyValueObjects.cs?name=KeyValueObjects)]

<span data-ttu-id="16fd0-338">Diese können dann im Domänen Modell verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="16fd0-338">These can then be used in the domain model:</span></span>

<!--
        public class Blog
        {
            public BlogKey Id { get; set; }
            public string Name { get; set; }

            public ICollection<Post> Posts { get; set; }
        }

        public class Post
        {
            public PostKey Id { get; set; }

            public string Title { get; set; }
            public string Content { get; set; }

            public BlogKey? BlogId { get; set; }
            public Blog Blog { get; set; }
        }
-->
[!code-csharp[KeyValueObjectsModel](../../../samples/core/Modeling/ValueConversions/KeyValueObjects.cs?name=KeyValueObjectsModel)]

<span data-ttu-id="16fd0-339">Beachten Sie, dass `Blog.Id` nicht versehentlich eine zugewiesen werden kann `PostKey` und `Post.Id` nicht versehentlich ein zugewiesen werden kann `BlogKey` .</span><span class="sxs-lookup"><span data-stu-id="16fd0-339">Notice that `Blog.Id` cannot accidentally be assigned a `PostKey`, and `Post.Id` cannot accidentally be assigned a `BlogKey`.</span></span> <span data-ttu-id="16fd0-340">Ebenso muss der `Post.BlogId` Fremdschlüssel Eigenschaft ein zugewiesen werden `BlogKey` .</span><span class="sxs-lookup"><span data-stu-id="16fd0-340">Similarly, the `Post.BlogId` foreign key property must be assigned a `BlogKey`.</span></span>

> [!NOTE]
> <span data-ttu-id="16fd0-341">Wenn dieses Muster angezeigt wird, bedeutet dies nicht, dass es empfohlen wird.</span><span class="sxs-lookup"><span data-stu-id="16fd0-341">Showing this pattern does not mean we recommend it.</span></span> <span data-ttu-id="16fd0-342">Überlegen Sie, ob diese Abstraktions Ebene das Entwicklungsverfahren unterstützt oder behindert.</span><span class="sxs-lookup"><span data-stu-id="16fd0-342">Carefully consider whether this level of abstraction is helping or hampering your development experience.</span></span> <span data-ttu-id="16fd0-343">Sie sollten auch die Verwendung von Navigationen und generierten Schlüsseln in Erwägung gezogen, anstatt direkt mit Schlüsselwerten umzugehen.</span><span class="sxs-lookup"><span data-stu-id="16fd0-343">Also, consider using navigations and generated keys instead of dealing with key values directly.</span></span>

<span data-ttu-id="16fd0-344">Diese Schlüsseleigenschaften können dann mithilfe von Wert Konvertern zugeordnet werden:</span><span class="sxs-lookup"><span data-stu-id="16fd0-344">These key properties can then be mapped using value converters:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                var blogKeyConverter = new ValueConverter<BlogKey, int>(
                    v => v.Id,
                    v => new BlogKey(v));

                modelBuilder.Entity<Blog>().Property(e => e.Id).HasConversion(blogKeyConverter);

                modelBuilder.Entity<Post>(
                    b =>
                        {
                            b.Property(e => e.Id).HasConversion(v => v.Id, v => new PostKey(v));
                            b.Property(e => e.BlogId).HasConversion(blogKeyConverter);
                        });
            }
-->
[!code-csharp[ConfigureKeyValueObjects](../../../samples/core/Modeling/ValueConversions/KeyValueObjects.cs?name=ConfigureKeyValueObjects)]

> [!NOTE]
> <span data-ttu-id="16fd0-345">Für Schlüsseleigenschaften mit Konvertierungen können derzeit keine generierten Schlüsselwerte verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="16fd0-345">Currently key properties with conversions cannot use generated key values.</span></span> <span data-ttu-id="16fd0-346">Stimmen Sie für das [GitHub-Problem #11597](https://github.com/dotnet/efcore/issues/11597) , dass diese Einschränkung entfernt werden soll.</span><span class="sxs-lookup"><span data-stu-id="16fd0-346">Vote for [GitHub issue #11597](https://github.com/dotnet/efcore/issues/11597) to have this limitation removed.</span></span>

### <a name="use-ulong-for-timestamprowversion"></a><span data-ttu-id="16fd0-347">Verwenden von ULONG für Timestamp/rowversion</span><span class="sxs-lookup"><span data-stu-id="16fd0-347">Use ulong for timestamp/rowversion</span></span>

<span data-ttu-id="16fd0-348">SQL Server unterstützt [die automatische voll](xref:core/saving/concurrency) ständige Parallelität mithilfe von [8-Byte-Binär `rowversion` / `timestamp` Spalten](/sql/t-sql/data-types/rowversion-transact-sql).</span><span class="sxs-lookup"><span data-stu-id="16fd0-348">SQL Server supports automatic [optimistic concurrency](xref:core/saving/concurrency) using [8-byte binary `rowversion`/`timestamp` columns](/sql/t-sql/data-types/rowversion-transact-sql).</span></span> <span data-ttu-id="16fd0-349">Diese werden stets mithilfe eines 8-Byte-Arrays aus der Datenbank gelesen und in diese geschrieben.</span><span class="sxs-lookup"><span data-stu-id="16fd0-349">These are always read from and written to the database using an 8-byte array.</span></span> <span data-ttu-id="16fd0-350">Byte Arrays sind jedoch ein änderbarer Verweistyp, der die Handhabung einigermaßen erschwert.</span><span class="sxs-lookup"><span data-stu-id="16fd0-350">However, byte arrays are a mutable reference type, which makes them somewhat painful to deal with.</span></span> <span data-ttu-id="16fd0-351">Mithilfe von Wert Konvertern kann `rowversion` stattdessen eine Eigenschaft zugeordnet `ulong` werden, die wesentlich besser geeignet und leichter zu verwenden ist als das Bytearray.</span><span class="sxs-lookup"><span data-stu-id="16fd0-351">Value converters allow the `rowversion` to instead be mapped to a `ulong` property, which is much more appropriate and easy to use than the byte array.</span></span> <span data-ttu-id="16fd0-352">Stellen Sie sich z. b. eine `Blog` Entität mit einem ULONG-Parallelitäts Token vor:</span><span class="sxs-lookup"><span data-stu-id="16fd0-352">For example, consider a `Blog` entity with a ulong concurrency token:</span></span>

<!--
        public class Blog
        {
            public int Id { get; set; }
            public string Name { get; set; }
            public ulong Version { get; set; }
        }
-->
[!code-csharp[ULongConcurrencyModel](../../../samples/core/Modeling/ValueConversions/ULongConcurrency.cs?name=ULongConcurrencyModel)]

<span data-ttu-id="16fd0-353">Dies kann `rowversion` mit einem Wert Konverter einer SQL Server-Spalte zugeordnet werden:</span><span class="sxs-lookup"><span data-stu-id="16fd0-353">This can be mapped to a SQL server `rowversion` column using a value converter:</span></span>

<!--
                modelBuilder.Entity<Blog>()
                    .Property(e => e.Version)
                    .IsRowVersion()
                    .HasConversion<byte[]>();
-->
[!code-csharp[ConfigureULongConcurrency](../../../samples/core/Modeling/ValueConversions/ULongConcurrency.cs?name=ConfigureULongConcurrency)]

### <a name="specify-the-datetimekind-when-reading-dates"></a><span data-ttu-id="16fd0-354">DateTime. Kind beim Lesen von Datumsangaben angeben</span><span class="sxs-lookup"><span data-stu-id="16fd0-354">Specify the DateTime.Kind when reading dates</span></span>

<span data-ttu-id="16fd0-355">SQL Server verwirft das- <xref:System.DateTime.Kind%2A?displayProperty=nameWithType> Flag, wenn ein <xref:System.DateTime> als oder gespeichert wird [`datetime`](/sql/t-sql/data-types/datetime-transact-sql) [`datetime2`](/sql/t-sql/data-types/datetime2-transact-sql) .</span><span class="sxs-lookup"><span data-stu-id="16fd0-355">SQL Server discards the <xref:System.DateTime.Kind%2A?displayProperty=nameWithType> flag when storing a <xref:System.DateTime> as a [`datetime`](/sql/t-sql/data-types/datetime-transact-sql) or [`datetime2`](/sql/t-sql/data-types/datetime2-transact-sql).</span></span> <span data-ttu-id="16fd0-356">Dies bedeutet, dass DateTime-Werte, die von der Datenbank zurückkommen, immer eine <xref:System.DateTimeKind> von aufweisen `Unspecified` .</span><span class="sxs-lookup"><span data-stu-id="16fd0-356">This means that DateTime values coming back from the database always have a <xref:System.DateTimeKind> of `Unspecified`.</span></span>

<span data-ttu-id="16fd0-357">Wert Konverter können auf zwei Arten verwendet werden, um damit umzugehen.</span><span class="sxs-lookup"><span data-stu-id="16fd0-357">Value converters can be used in two ways to deal with this.</span></span> <span data-ttu-id="16fd0-358">Zuerst verfügt EF Core über einen Wert Konverter, der einen nicht transparenten 8-Byte-Wert erstellt, der das-Flag beibehält `Kind` .</span><span class="sxs-lookup"><span data-stu-id="16fd0-358">First, EF Core has a value converter that creates an 8-byte opaque value which preserves the `Kind` flag.</span></span> <span data-ttu-id="16fd0-359">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="16fd0-359">For example:</span></span>

<!--
                modelBuilder.Entity<Post>()
                    .Property(e => e.PostedOn)
                    .HasConversion<long>();
-->
[!code-csharp[ConfigurePreserveDateTimeKind1](../../../samples/core/Modeling/ValueConversions/PreserveDateTimeKind.cs?name=ConfigurePreserveDateTimeKind1)]

<span data-ttu-id="16fd0-360">Dadurch können DateTime-Werte mit unterschiedlichen `Kind` Flags in der Datenbank gemischt werden.</span><span class="sxs-lookup"><span data-stu-id="16fd0-360">This allows DateTime values with different `Kind` flags to be mixed in the database.</span></span>

<span data-ttu-id="16fd0-361">Das Problem bei diesem Ansatz besteht darin, dass die Datenbank nicht mehr über erkennbare- `datetime` oder- `datetime2` Spalten verfügt.</span><span class="sxs-lookup"><span data-stu-id="16fd0-361">The problem with this approach is that the database no longer has recognizable `datetime` or `datetime2` columns.</span></span> <span data-ttu-id="16fd0-362">Stattdessen ist es üblich, immer die UTC-Zeit (oder seltener, immer Ortszeit) zu speichern und dann entweder das `Kind` Flag zu ignorieren oder es mit einem Wert Konverter auf den entsprechenden Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="16fd0-362">So instead it is common to always store UTC time (or, less commonly, always local time) and then either ignore the `Kind` flag or set it to the appropriate value using a value converter.</span></span> <span data-ttu-id="16fd0-363">Beispielsweise stellt der Konverter unten sicher, dass der `DateTime` aus der Datenbank gelesene Wert Folgendes hat <xref:System.DateTimeKind> `UTC` :</span><span class="sxs-lookup"><span data-stu-id="16fd0-363">For example, the converter below ensures that the `DateTime` value read from the database will have the <xref:System.DateTimeKind> `UTC`:</span></span>

<!--
                modelBuilder.Entity<Post>()
                    .Property(e => e.LastUpdated)
                    .HasConversion(
                        v => v,
                        v => new DateTime(v.Ticks, DateTimeKind.Utc));
-->
[!code-csharp[ConfigurePreserveDateTimeKind2](../../../samples/core/Modeling/ValueConversions/PreserveDateTimeKind.cs?name=ConfigurePreserveDateTimeKind2)]

<span data-ttu-id="16fd0-364">Wenn eine Mischung aus lokalen und UTC-Werten in Entitäts Instanzen festgelegt wird, kann der Konverter verwendet werden, um vor dem Einfügen entsprechend zu konvertieren.</span><span class="sxs-lookup"><span data-stu-id="16fd0-364">If a mix of local and UTC values are being set in entity instances, then the converter can be used to convert appropriately before inserting.</span></span> <span data-ttu-id="16fd0-365">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="16fd0-365">For example:</span></span>

<!--
                modelBuilder.Entity<Post>()
                    .Property(e => e.LastUpdated)
                    .HasConversion(
                        v => v.ToUniversalTime(),
                        v => new DateTime(v.Ticks, DateTimeKind.Utc));
-->
[!code-csharp[ConfigurePreserveDateTimeKind3](../../../samples/core/Modeling/ValueConversions/PreserveDateTimeKind.cs?name=ConfigurePreserveDateTimeKind3)]

> [!NOTE]
> <span data-ttu-id="16fd0-366">Achten Sie sorgfältig darauf, den gesamten Datenbankzugriffs Code so zu vereinheitlichen, dass die UTC-Zeit immer verwendet wird</span><span class="sxs-lookup"><span data-stu-id="16fd0-366">Carefully consider unifying all database access code to use UTC time all the time, only dealing with local time when presenting data to users.</span></span>

### <a name="use-case-insensitive-string-keys"></a><span data-ttu-id="16fd0-367">Zeichen folgen Schlüssel mit Groß-/Kleinschreibung verwenden</span><span class="sxs-lookup"><span data-stu-id="16fd0-367">Use case-insensitive string keys</span></span>

<span data-ttu-id="16fd0-368">Einige Datenbanken, einschließlich SQL Server, führen standardmäßig Zeichen folgen Vergleiche ohne Berücksichtigung der Groß-und Kleinschreibung durch</span><span class="sxs-lookup"><span data-stu-id="16fd0-368">Some databases, including SQL Server, perform case-insensitive string comparisons by default.</span></span> <span data-ttu-id="16fd0-369">.NET hingegen führt standardmäßig die Groß-/Kleinschreibung für Zeichen folgen Vergleiche durch.</span><span class="sxs-lookup"><span data-stu-id="16fd0-369">.NET, on the other hand, performs case-sensitive string comparisons by default.</span></span> <span data-ttu-id="16fd0-370">Dies bedeutet, dass ein Fremdschlüssel Wert wie "dotnet" mit dem Primärschlüssel Wert "dotnet" auf SQL Server, aber nicht mit dem Wert in EF Core abgeglichen wird.</span><span class="sxs-lookup"><span data-stu-id="16fd0-370">This means that a foreign key value like "DotNet" will match the primary key value "dotnet" on SQL Server, but will not match it in EF Core.</span></span> <span data-ttu-id="16fd0-371">Ein Wert Vergleich für Schlüssel kann verwendet werden, um zu erzwingen, dass EF Core groß-und Kleinschreibung für Zeichen folgen Vergleiche wie in der Datenbank nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="16fd0-371">A value comparer for keys can be used to force EF Core into case-insensitive string comparisons like in the database.</span></span> <span data-ttu-id="16fd0-372">Stellen Sie sich beispielsweise ein Blog-/Post-Modell mit Zeichen folgen Schlüsseln vor:</span><span class="sxs-lookup"><span data-stu-id="16fd0-372">For example, consider a blog/posts model with string keys:</span></span>

<!--
        public class Blog
        {
            public string Id { get; set; }
            public string Name { get; set; }

            public ICollection<Post> Posts { get; set; }
        }

        public class Post
        {
            public string Id { get; set; }
            public string Title { get; set; }
            public string Content { get; set; }

            public string BlogId { get; set; }
            public Blog Blog { get; set; }
        }
-->
[!code-csharp[CaseInsensitiveStringsModel](../../../samples/core/Modeling/ValueConversions/CaseInsensitiveStrings.cs?name=CaseInsensitiveStringsModel)]

<span data-ttu-id="16fd0-373">Dies funktioniert nicht wie erwartet, wenn einige der Werte eine andere Schreibweise `Post.BlogId` aufweisen.</span><span class="sxs-lookup"><span data-stu-id="16fd0-373">This will not work as expected if some of the `Post.BlogId` values have different casing.</span></span> <span data-ttu-id="16fd0-374">Die Fehler, die dadurch verursacht werden, hängen davon ab, wie die Anwendung ausgeführt wird, aber in der Regel Diagramme von Objekten, die nicht ordnungsgemäß korrigiert werden, und/oder Updates, die fehlschlagen, weil der FK [-](xref:core/change-tracking/relationship-changes) Wert falsch ist.</span><span class="sxs-lookup"><span data-stu-id="16fd0-374">The errors caused by this will depend on what the application is doing, but typically involve graphs of objects that are not [fixed-up](xref:core/change-tracking/relationship-changes) correctly, and/or updates that fail because the FK value is wrong.</span></span> <span data-ttu-id="16fd0-375">Ein Wert Vergleich kann verwendet werden, um Folgendes zu beheben:</span><span class="sxs-lookup"><span data-stu-id="16fd0-375">A value comparer can be used to correct this:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                var comparer = new ValueComparer<string>(
                    (l, r) => string.Equals(l, r, StringComparison.OrdinalIgnoreCase),
                    v => v.ToUpper().GetHashCode(),
                    v => v);

                modelBuilder.Entity<Blog>()
                    .Property(e => e.Id)
                    .Metadata.SetValueComparer(comparer);

                modelBuilder.Entity<Post>(
                    b =>
                        {
                            b.Property(e => e.Id).Metadata.SetValueComparer(comparer);
                            b.Property(e => e.BlogId).Metadata.SetValueComparer(comparer);
                        });
            }
-->
[!code-csharp[ConfigureCaseInsensitiveStrings](../../../samples/core/Modeling/ValueConversions/CaseInsensitiveStrings.cs?name=ConfigureCaseInsensitiveStrings)]

> [!NOTE]
> <span data-ttu-id="16fd0-376">.Net-Zeichen folgen Vergleiche und Daten Bank Zeichen folgen Vergleiche können sich in mehr als nur der Groß-/Kleinschreibung</span><span class="sxs-lookup"><span data-stu-id="16fd0-376">.NET string comparisons and database string comparisons can differ in more than just case sensitivity.</span></span> <span data-ttu-id="16fd0-377">Dieses Muster funktioniert für einfache ASCII-Schlüssel, kann jedoch für Schlüssel mit beliebigen kulturspezifischen Zeichen fehlschlagen.</span><span class="sxs-lookup"><span data-stu-id="16fd0-377">This pattern works for simple ASCII keys, but may fail for keys with any kind of culture-specific characters.</span></span> <span data-ttu-id="16fd0-378">Weitere Informationen finden Sie unter [Sortierungen und Groß-/Kleinschreibung](xref:core/miscellaneous/collations-and-case-sensitivity) .</span><span class="sxs-lookup"><span data-stu-id="16fd0-378">See [Collations and Case Sensitivity](xref:core/miscellaneous/collations-and-case-sensitivity) for more information.</span></span>

### <a name="handle-fixed-length-database-strings"></a><span data-ttu-id="16fd0-379">Verarbeiten von Daten Bank Zeichenfolgen fester Länge</span><span class="sxs-lookup"><span data-stu-id="16fd0-379">Handle fixed-length database strings</span></span>

<span data-ttu-id="16fd0-380">Im vorherigen Beispiel wurde kein Wert Konverter benötigt.</span><span class="sxs-lookup"><span data-stu-id="16fd0-380">The previous example did not need a value converter.</span></span> <span data-ttu-id="16fd0-381">Ein Konverter kann jedoch für Daten Bank Zeichen folgen Typen mit fester Länge wie oder nützlich sein `char(20)` `nchar(20)` .</span><span class="sxs-lookup"><span data-stu-id="16fd0-381">However, a converter can be useful for fixed-length database string types like `char(20)` or `nchar(20)`.</span></span> <span data-ttu-id="16fd0-382">Zeichen folgen mit fester Länge werden in der vollständigen Länge aufgefüllt, wenn ein Wert in die Datenbank eingefügt wird.</span><span class="sxs-lookup"><span data-stu-id="16fd0-382">Fixed-length strings are padded to their full length whenever a value is inserted into the database.</span></span> <span data-ttu-id="16fd0-383">Dies bedeutet, dass der Schlüsselwert " `dotnet` " aus der Datenbank als "" gelesen wird `dotnet..............` , wobei `.` ein Leerzeichen darstellt.</span><span class="sxs-lookup"><span data-stu-id="16fd0-383">This means that a key value of "`dotnet`" will be read back from the database as "`dotnet..............`", where `.` represents a space character.</span></span> <span data-ttu-id="16fd0-384">Dies wird dann nicht ordnungsgemäß mit Schlüsselwerten verglichen, die nicht aufgefüllt werden.</span><span class="sxs-lookup"><span data-stu-id="16fd0-384">This will then not compare correctly with key values that are not padded.</span></span>

<span data-ttu-id="16fd0-385">Ein Wert Konverter kann verwendet werden, um die Auffüll Zeichen beim Lesen von Schlüsselwerten zu kürzen.</span><span class="sxs-lookup"><span data-stu-id="16fd0-385">A value converter can be used to trim the padding when reading key values.</span></span> <span data-ttu-id="16fd0-386">Dies kann im vorherigen Beispiel mit dem Wert Vergleich kombiniert werden, um die Groß-/Kleinschreibung ohne Berücksichtigung der Groß-/Kleinschreibung zu vergleichen.</span><span class="sxs-lookup"><span data-stu-id="16fd0-386">This can be combined with the value comparer in the previous example to compare fixed length case-insensitive ASCII keys correctly.</span></span> <span data-ttu-id="16fd0-387">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="16fd0-387">For example:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                var converter = new ValueConverter<string, string>(
                    v => v,
                    v => v.Trim());
                
                var comparer = new ValueComparer<string>(
                    (l, r) => string.Equals(l, r, StringComparison.OrdinalIgnoreCase),
                    v => v.ToUpper().GetHashCode(),
                    v => v);

                modelBuilder.Entity<Blog>()
                    .Property(e => e.Id)
                    .HasColumnType("char(20)")
                    .HasConversion(converter, comparer);

                modelBuilder.Entity<Post>(
                    b =>
                        {
                            b.Property(e => e.Id).HasColumnType("char(20)").HasConversion(converter, comparer);
                            b.Property(e => e.BlogId).HasColumnType("char(20)").HasConversion(converter, comparer);
                        });
            }
-->
[!code-csharp[ConfigureFixedLengthStrings](../../../samples/core/Modeling/ValueConversions/FixedLengthStrings.cs?name=ConfigureFixedLengthStrings)]

### <a name="encrypt-property-values"></a><span data-ttu-id="16fd0-388">Verschlüsseln von Eigenschafts Werten</span><span class="sxs-lookup"><span data-stu-id="16fd0-388">Encrypt property values</span></span>

<span data-ttu-id="16fd0-389">Wert Konverter können verwendet werden, um Eigenschaftswerte vor dem Senden an die Datenbank zu verschlüsseln und Sie dann auf dem Weg zu entschlüsseln. Verwenden Sie z. b. die Zeichen folgen Umkehrung als Ersatz für einen echten Verschlüsselungsalgorithmus:</span><span class="sxs-lookup"><span data-stu-id="16fd0-389">Value converters can be used to encrypt property values before sending them to the database, and then decrypt them on the way out. For example, using string reversal as a substitute for a real encryption algorithm:</span></span>

<!--
                modelBuilder.Entity<User>().Property(e => e.Password).HasConversion(
                    v => new string(v.Reverse().ToArray()),
                    v => new string(v.Reverse().ToArray()));
-->
[!code-csharp[ConfigureEncryptPropertyValues](../../../samples/core/Modeling/ValueConversions/EncryptPropertyValues.cs?name=ConfigureEncryptPropertyValues)]

> [!NOTE]
> <span data-ttu-id="16fd0-390">Es gibt derzeit keine Möglichkeit, einen Verweis auf den aktuellen dbcontext oder einen anderen Sitzungszustand innerhalb eines Wert Konverters zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="16fd0-390">There is currently no way to get a reference to the current DbContext, or other session state, from within a value converter.</span></span> <span data-ttu-id="16fd0-391">Dies schränkt die Arten der Verschlüsselung ein, die verwendet werden können.</span><span class="sxs-lookup"><span data-stu-id="16fd0-391">This limits the kinds of encryption that can be used.</span></span> <span data-ttu-id="16fd0-392">Stimmen Sie für das [GitHub-Problem #11597](https://github.com/dotnet/efcore/issues/12205) , dass diese Einschränkung entfernt werden soll.</span><span class="sxs-lookup"><span data-stu-id="16fd0-392">Vote for [GitHub issue #11597](https://github.com/dotnet/efcore/issues/12205) to have this limitation removed.</span></span>

> [!WARNING]
> <span data-ttu-id="16fd0-393">Stellen Sie sicher, dass Sie alle Implikationen verstehen, wenn Sie eine eigene Verschlüsselung zum Schutz sensibler Daten ausführen.</span><span class="sxs-lookup"><span data-stu-id="16fd0-393">Make sure to understand all the implications if you roll your own encryption to protect sensitive data.</span></span> <span data-ttu-id="16fd0-394">Verwenden Sie stattdessen vorgefertigte Verschlüsselungsmechanismen, z. b. [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) auf SQL Server.</span><span class="sxs-lookup"><span data-stu-id="16fd0-394">Consider instead using pre-built encryption mechanisms, such as [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) on SQL Server.</span></span>

## <a name="limitations"></a><span data-ttu-id="16fd0-395">Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="16fd0-395">Limitations</span></span>

<span data-ttu-id="16fd0-396">Es gibt einige bekannte aktuelle Einschränkungen des Value-Konvertierungs Systems:</span><span class="sxs-lookup"><span data-stu-id="16fd0-396">There are a few known current limitations of the value conversion system:</span></span>

* <span data-ttu-id="16fd0-397">Es gibt derzeit keine Möglichkeit, an einem Ort anzugeben, dass jede Eigenschaft eines bestimmten Typs denselben Wert Konverter verwenden muss.</span><span class="sxs-lookup"><span data-stu-id="16fd0-397">There is currently no way to specify in one place that every property of a given type must use the same value converter.</span></span> <span data-ttu-id="16fd0-398">Bitte stimmen 👍 Sie () auf [GitHub-Problem #10784](https://github.com/dotnet/efcore/issues/10784) ab, wenn dies etwas ist, das Sie benötigen.</span><span class="sxs-lookup"><span data-stu-id="16fd0-398">Please vote (👍) for [GitHub issue #10784](https://github.com/dotnet/efcore/issues/10784) if this is something you need.</span></span>
* <span data-ttu-id="16fd0-399">Wie bereits erwähnt, `null` kann nicht konvertiert werden.</span><span class="sxs-lookup"><span data-stu-id="16fd0-399">As noted above, `null` cannot be converted.</span></span> <span data-ttu-id="16fd0-400">Bitte stimmen 👍 Sie () auf [GitHub-Problem #13850](https://github.com/dotnet/efcore/issues/13850) ab, wenn dies etwas ist, das Sie benötigen.</span><span class="sxs-lookup"><span data-stu-id="16fd0-400">Please vote (👍) for [GitHub issue #13850](https://github.com/dotnet/efcore/issues/13850) if this is something you need.</span></span>
* <span data-ttu-id="16fd0-401">Zurzeit gibt es keine Möglichkeit, eine Konvertierung einer Eigenschaft in mehrere Spalten zu verteilen (oder umgekehrt).</span><span class="sxs-lookup"><span data-stu-id="16fd0-401">There is currently no way to spread a conversion of one property to multiple columns or vice-versa.</span></span> <span data-ttu-id="16fd0-402">Bitte stimmen 👍 Sie () auf [GitHub-Problem #13947](https://github.com/dotnet/efcore/issues/13947) ab, wenn dies etwas ist, das Sie benötigen.</span><span class="sxs-lookup"><span data-stu-id="16fd0-402">Please vote (👍) for [GitHub issue #13947](https://github.com/dotnet/efcore/issues/13947) if this is something you need.</span></span>
* <span data-ttu-id="16fd0-403">Die Generierung von Werten wird für die meisten durch Wert Konverter zugeordneten Schlüssel nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="16fd0-403">Value generation is not supported for most keys mapped through value converters.</span></span> <span data-ttu-id="16fd0-404">Bitte stimmen 👍 Sie () auf [GitHub-Problem #11597](https://github.com/dotnet/efcore/issues/11597) ab, wenn dies etwas ist, das Sie benötigen.</span><span class="sxs-lookup"><span data-stu-id="16fd0-404">Please vote (👍) for [GitHub issue #11597](https://github.com/dotnet/efcore/issues/11597) if this is something you need.</span></span>
* <span data-ttu-id="16fd0-405">Wert Konvertierungen können nicht auf die aktuelle dbcontext-Instanz verweisen.</span><span class="sxs-lookup"><span data-stu-id="16fd0-405">Value conversions cannot reference the current DbContext instance.</span></span> <span data-ttu-id="16fd0-406">Bitte stimmen 👍 Sie () auf [GitHub-Problem #11597](https://github.com/dotnet/efcore/issues/12205) ab, wenn dies etwas ist, das Sie benötigen.</span><span class="sxs-lookup"><span data-stu-id="16fd0-406">Please vote (👍) for [GitHub issue #11597](https://github.com/dotnet/efcore/issues/12205) if this is something you need.</span></span>

<span data-ttu-id="16fd0-407">Das Entfernen dieser Einschränkungen wird in zukünftigen Versionen berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="16fd0-407">Removal of these limitations is being considered for future releases.</span></span>
