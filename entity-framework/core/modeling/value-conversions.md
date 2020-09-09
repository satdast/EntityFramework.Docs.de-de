---
title: Wert Konvertierungen-EF Core
description: Konfigurieren von Wert Konvertern in einem Entity Framework Core Modell
author: ajcvickers
ms.date: 02/19/2018
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
uid: core/modeling/value-conversions
ms.openlocfilehash: 79e54392bf5503b4b651f25ce6e5fc63d418df90
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616659"
---
# <a name="value-conversions"></a><span data-ttu-id="c7105-103">Wertkonvertierungen</span><span class="sxs-lookup"><span data-stu-id="c7105-103">Value Conversions</span></span>

> [!NOTE]  
> <span data-ttu-id="c7105-104">Dieses Feature ist neu in EF Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="c7105-104">This feature is new in EF Core 2.1.</span></span>

<span data-ttu-id="c7105-105">Mithilfe von Wert Konvertern können Eigenschaftswerte beim Lesen aus der Datenbank oder beim Schreiben in die Datenbank konvertiert werden.</span><span class="sxs-lookup"><span data-stu-id="c7105-105">Value converters allow property values to be converted when reading from or writing to the database.</span></span> <span data-ttu-id="c7105-106">Diese Konvertierung kann von einem Wert in einen anderen desselben Typs (z. b. das Verschlüsseln von Zeichen folgen) oder von einem Wert eines Typs zu einem Wert eines anderen Typs erfolgen (z. b. beim Konvertieren von Enumerationswerten in und aus Zeichen folgen in der Datenbank).</span><span class="sxs-lookup"><span data-stu-id="c7105-106">This conversion can be from one value to another of the same type (for example, encrypting strings) or from a value of one type to a value of another type (for example, converting enum values to and from strings in the database.)</span></span>

## <a name="fundamentals"></a><span data-ttu-id="c7105-107">Grundlagen</span><span class="sxs-lookup"><span data-stu-id="c7105-107">Fundamentals</span></span>

<span data-ttu-id="c7105-108">Wert Konverter werden in Bezug auf einen `ModelClrType` und einen angegeben `ProviderClrType` .</span><span class="sxs-lookup"><span data-stu-id="c7105-108">Value converters are specified in terms of a `ModelClrType` and a `ProviderClrType`.</span></span> <span data-ttu-id="c7105-109">Der Modelltyp ist der .NET-Typ der-Eigenschaft im-Entitätstyp.</span><span class="sxs-lookup"><span data-stu-id="c7105-109">The model type is the .NET type of the property in the entity type.</span></span> <span data-ttu-id="c7105-110">Der Anbietertyp ist der .NET-Typ, der vom Datenbankanbieter verstanden wird.</span><span class="sxs-lookup"><span data-stu-id="c7105-110">The provider type is the .NET type understood by the database provider.</span></span> <span data-ttu-id="c7105-111">Um z. b. Enumerationen als Zeichen folgen in der Datenbank zu speichern, ist der Modelltyp der Typ der Enumeration, und der Anbietertyp ist `String` .</span><span class="sxs-lookup"><span data-stu-id="c7105-111">For example, to save enums as strings in the database, the model type is the type of the enum, and the provider type is `String`.</span></span> <span data-ttu-id="c7105-112">Diese beiden Typen können identisch sein.</span><span class="sxs-lookup"><span data-stu-id="c7105-112">These two types can be the same.</span></span>

<span data-ttu-id="c7105-113">Konvertierungen werden mithilfe `Func` von zwei Ausdrucks Baumstrukturen definiert: einer von `ModelClrType` `ProviderClrType` und der andere von `ProviderClrType` bis `ModelClrType` .</span><span class="sxs-lookup"><span data-stu-id="c7105-113">Conversions are defined using two `Func` expression trees: one from `ModelClrType` to `ProviderClrType` and the other from `ProviderClrType` to `ModelClrType`.</span></span> <span data-ttu-id="c7105-114">Ausdrucks Baumstrukturen werden verwendet, damit Sie für effiziente Konvertierungen in den Datenbankzugriffs Code kompiliert werden können.</span><span class="sxs-lookup"><span data-stu-id="c7105-114">Expression trees are used so that they can be compiled into the database access code for efficient conversions.</span></span> <span data-ttu-id="c7105-115">Bei komplexen Konvertierungen kann die Ausdrucks Baumstruktur ein einfacher aufrufungs Vorgang für eine Methode sein, die die Konvertierung ausführt.</span><span class="sxs-lookup"><span data-stu-id="c7105-115">For complex conversions, the expression tree may be a simple call to a method that performs the conversion.</span></span>

## <a name="configuring-a-value-converter"></a><span data-ttu-id="c7105-116">Konfigurieren eines Wert Konverters</span><span class="sxs-lookup"><span data-stu-id="c7105-116">Configuring a value converter</span></span>

<span data-ttu-id="c7105-117">Wert Konvertierungen werden für Eigenschaften in der definiert `OnModelCreating` `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="c7105-117">Value conversions are defined on properties in the `OnModelCreating` of your `DbContext`.</span></span> <span data-ttu-id="c7105-118">Stellen Sie sich z. b. eine Aufzählung und einen Entitätstyp vor</span><span class="sxs-lookup"><span data-stu-id="c7105-118">For example, consider an enum and entity type defined as:</span></span>

``` csharp
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
```

<span data-ttu-id="c7105-119">Anschließend können Konvertierungen in definiert werden `OnModelCreating` , um die Enumerationswerte als Zeichen folgen (z. b. "Esel", "maulzeichen",...) in der Datenbank zu speichern:</span><span class="sxs-lookup"><span data-stu-id="c7105-119">Then conversions can be defined in `OnModelCreating` to store the enum values as strings (for example, "Donkey", "Mule", ...) in the database:</span></span>

``` csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Rider>()
        .Property(e => e.Mount)
        .HasConversion(
            v => v.ToString(),
            v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));
}
```

> [!NOTE]  
> <span data-ttu-id="c7105-120">Ein `null` Wert wird nie an einen Wert Konverter übermittelt.</span><span class="sxs-lookup"><span data-stu-id="c7105-120">A `null` value will never be passed to a value converter.</span></span> <span data-ttu-id="c7105-121">Dies vereinfacht die Implementierung von Konvertierungen und ermöglicht die gemeinsame Nutzung zwischen NULL-Werten und nicht auf NULL festleg baren Eigenschaften.</span><span class="sxs-lookup"><span data-stu-id="c7105-121">This makes the implementation of conversions easier and allows them to be shared amongst nullable and non-nullable properties.</span></span>

## <a name="the-valueconverter-class"></a><span data-ttu-id="c7105-122">Die ValueConverter-Klasse</span><span class="sxs-lookup"><span data-stu-id="c7105-122">The ValueConverter class</span></span>

<span data-ttu-id="c7105-123">`HasConversion`Wenn Sie wie oben gezeigt aufrufen, wird eine `ValueConverter` -Instanz erstellt und für die-Eigenschaft festgelegt.</span><span class="sxs-lookup"><span data-stu-id="c7105-123">Calling `HasConversion` as shown above will create a `ValueConverter` instance and set it on the property.</span></span> <span data-ttu-id="c7105-124">`ValueConverter`Stattdessen kann explizit erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="c7105-124">The `ValueConverter` can instead be created explicitly.</span></span> <span data-ttu-id="c7105-125">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="c7105-125">For example:</span></span>

``` csharp
var converter = new ValueConverter<EquineBeast, string>(
    v => v.ToString(),
    v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```

<span data-ttu-id="c7105-126">Dies kann hilfreich sein, wenn mehrere Eigenschaften dieselbe Konvertierung verwenden.</span><span class="sxs-lookup"><span data-stu-id="c7105-126">This can be useful when multiple properties use the same conversion.</span></span>

> [!NOTE]  
> <span data-ttu-id="c7105-127">Es gibt derzeit keine Möglichkeit, an einem Ort anzugeben, dass jede Eigenschaft eines bestimmten Typs denselben Wert Konverter verwenden muss.</span><span class="sxs-lookup"><span data-stu-id="c7105-127">There is currently no way to specify in one place that every property of a given type must use the same value converter.</span></span> <span data-ttu-id="c7105-128">Diese Funktion wird für eine zukünftige Version in Erwägung gezogen.</span><span class="sxs-lookup"><span data-stu-id="c7105-128">This feature will be considered for a future release.</span></span>

## <a name="built-in-converters"></a><span data-ttu-id="c7105-129">Integrierte Konverter</span><span class="sxs-lookup"><span data-stu-id="c7105-129">Built-in converters</span></span>

<span data-ttu-id="c7105-130">EF Core enthält eine Reihe vordefinierter `ValueConverter` Klassen, die im- `Microsoft.EntityFrameworkCore.Storage.ValueConversion` Namespace enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="c7105-130">EF Core ships with a set of pre-defined `ValueConverter` classes, found in the `Microsoft.EntityFrameworkCore.Storage.ValueConversion` namespace.</span></span> <span data-ttu-id="c7105-131">Dies sind:</span><span class="sxs-lookup"><span data-stu-id="c7105-131">These are:</span></span>

* <span data-ttu-id="c7105-132">`BoolToZeroOneConverter` -Bool in NULL und eins</span><span class="sxs-lookup"><span data-stu-id="c7105-132">`BoolToZeroOneConverter` - Bool to zero and one</span></span>
* <span data-ttu-id="c7105-133">`BoolToStringConverter` -Bool in Zeichen folgen wie "Y" und "N"</span><span class="sxs-lookup"><span data-stu-id="c7105-133">`BoolToStringConverter` - Bool to strings such as "Y" and "N"</span></span>
* <span data-ttu-id="c7105-134">`BoolToTwoValuesConverter` -Bool auf zwei beliebige Werte</span><span class="sxs-lookup"><span data-stu-id="c7105-134">`BoolToTwoValuesConverter` - Bool to any two values</span></span>
* <span data-ttu-id="c7105-135">`BytesToStringConverter` -Bytearray in Base64-codierte Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="c7105-135">`BytesToStringConverter` - Byte array to Base64-encoded string</span></span>
* <span data-ttu-id="c7105-136">`CastingConverter` -Konvertierungen, die nur eine Typumwandlung erfordern</span><span class="sxs-lookup"><span data-stu-id="c7105-136">`CastingConverter` - Conversions that require only a type cast</span></span>
* <span data-ttu-id="c7105-137">`CharToStringConverter` -Char in eine Zeichenfolge mit einem Zeichen</span><span class="sxs-lookup"><span data-stu-id="c7105-137">`CharToStringConverter` - Char to single character string</span></span>
* <span data-ttu-id="c7105-138">`DateTimeOffsetToBinaryConverter` -DateTimeOffset in binary-codierter 64-Bit-Wert</span><span class="sxs-lookup"><span data-stu-id="c7105-138">`DateTimeOffsetToBinaryConverter` - DateTimeOffset to binary-encoded 64-bit value</span></span>
* <span data-ttu-id="c7105-139">`DateTimeOffsetToBytesConverter` -DateTimeOffset in Bytearray</span><span class="sxs-lookup"><span data-stu-id="c7105-139">`DateTimeOffsetToBytesConverter` - DateTimeOffset to byte array</span></span>
* <span data-ttu-id="c7105-140">`DateTimeOffsetToStringConverter` -DateTimeOffset in Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="c7105-140">`DateTimeOffsetToStringConverter` - DateTimeOffset to string</span></span>
* <span data-ttu-id="c7105-141">`DateTimeToBinaryConverter` -DateTime-to-64-Bit-Wert, einschließlich DateTimeKind</span><span class="sxs-lookup"><span data-stu-id="c7105-141">`DateTimeToBinaryConverter` - DateTime to 64-bit value including DateTimeKind</span></span>
* <span data-ttu-id="c7105-142">`DateTimeToStringConverter` -DateTime zu String</span><span class="sxs-lookup"><span data-stu-id="c7105-142">`DateTimeToStringConverter` - DateTime to string</span></span>
* <span data-ttu-id="c7105-143">`DateTimeToTicksConverter` -DateTime zu Ticks</span><span class="sxs-lookup"><span data-stu-id="c7105-143">`DateTimeToTicksConverter` - DateTime to ticks</span></span>
* <span data-ttu-id="c7105-144">`EnumToNumberConverter` -Enumeration zur zugrunde liegenden Zahl</span><span class="sxs-lookup"><span data-stu-id="c7105-144">`EnumToNumberConverter` - Enum to underlying number</span></span>
* <span data-ttu-id="c7105-145">`EnumToStringConverter` -Enum in Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="c7105-145">`EnumToStringConverter` - Enum to string</span></span>
* <span data-ttu-id="c7105-146">`GuidToBytesConverter` -GUID zu Bytearray</span><span class="sxs-lookup"><span data-stu-id="c7105-146">`GuidToBytesConverter` - Guid to byte array</span></span>
* <span data-ttu-id="c7105-147">`GuidToStringConverter` -GUID zu Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="c7105-147">`GuidToStringConverter` - Guid to string</span></span>
* <span data-ttu-id="c7105-148">`NumberToBytesConverter` -Beliebiger numerischer Wert zum Bytearray</span><span class="sxs-lookup"><span data-stu-id="c7105-148">`NumberToBytesConverter` - Any numerical value to byte array</span></span>
* <span data-ttu-id="c7105-149">`NumberToStringConverter` -Beliebiger numerischer Wert zu Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="c7105-149">`NumberToStringConverter` - Any numerical value to string</span></span>
* <span data-ttu-id="c7105-150">`StringToBytesConverter` -Zeichenfolge in UTF8-bytes</span><span class="sxs-lookup"><span data-stu-id="c7105-150">`StringToBytesConverter` - String to UTF8 bytes</span></span>
* <span data-ttu-id="c7105-151">`TimeSpanToStringConverter` -TimeSpan zu Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="c7105-151">`TimeSpanToStringConverter` - TimeSpan to string</span></span>
* <span data-ttu-id="c7105-152">`TimeSpanToTicksConverter` -TimeSpan in Ticks</span><span class="sxs-lookup"><span data-stu-id="c7105-152">`TimeSpanToTicksConverter` - TimeSpan to ticks</span></span>

<span data-ttu-id="c7105-153">Beachten Sie, dass `EnumToStringConverter` in dieser Liste enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="c7105-153">Notice that `EnumToStringConverter` is included in this list.</span></span> <span data-ttu-id="c7105-154">Dies bedeutet, dass die Konvertierung nicht explizit angegeben werden muss, wie oben gezeigt.</span><span class="sxs-lookup"><span data-stu-id="c7105-154">This means that there is no need to specify the conversion explicitly, as shown above.</span></span> <span data-ttu-id="c7105-155">Verwenden Sie stattdessen einfach den integrierten Konverter:</span><span class="sxs-lookup"><span data-stu-id="c7105-155">Instead, just use the built-in converter:</span></span>

``` csharp
var converter = new EnumToStringConverter<EquineBeast>();

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```

<span data-ttu-id="c7105-156">Beachten Sie, dass alle integrierten Konverter zustandslos sind, sodass eine einzelne Instanz auf sichere Weise von mehreren Eigenschaften gemeinsam genutzt werden kann.</span><span class="sxs-lookup"><span data-stu-id="c7105-156">Note that all the built-in converters are stateless and so a single instance can be safely shared by multiple properties.</span></span>

## <a name="pre-defined-conversions"></a><span data-ttu-id="c7105-157">Vordefinierte Konvertierungen</span><span class="sxs-lookup"><span data-stu-id="c7105-157">Pre-defined conversions</span></span>

<span data-ttu-id="c7105-158">Für allgemeine Konvertierungen, bei denen ein integrierter Konverter vorhanden ist, muss der Konverter nicht explizit angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="c7105-158">For common conversions for which a built-in converter exists there is no need to specify the converter explicitly.</span></span> <span data-ttu-id="c7105-159">Stattdessen konfigurieren Sie einfach den zu verwendenden Anbietertyp, und EF verwendet automatisch den entsprechenden integrierten Konverter.</span><span class="sxs-lookup"><span data-stu-id="c7105-159">Instead, just configure which provider type should be used and EF will automatically use the appropriate built-in converter.</span></span> <span data-ttu-id="c7105-160">Konvertierungen von Enum zu Zeichen folgen werden als Beispiel oben verwendet, EF führt dies jedoch automatisch aus, wenn der Anbietertyp konfiguriert ist:</span><span class="sxs-lookup"><span data-stu-id="c7105-160">Enum to string conversions are used as an example above, but EF will actually do this automatically if the provider type is configured:</span></span>

``` csharp
modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion<string>();
```

<span data-ttu-id="c7105-161">Dies kann auch durch explizites angeben des Spalten Typs erreicht werden.</span><span class="sxs-lookup"><span data-stu-id="c7105-161">The same thing can be achieved by explicitly specifying the column type.</span></span> <span data-ttu-id="c7105-162">Beispielsweise, wenn der Entitätstyp wie folgt definiert ist:</span><span class="sxs-lookup"><span data-stu-id="c7105-162">For example, if the entity type is defined like so:</span></span>

``` csharp
public class Rider
{
    public int Id { get; set; }

    [Column(TypeName = "nvarchar(24)")]
    public EquineBeast Mount { get; set; }
}
```

<span data-ttu-id="c7105-163">Die Enumerationswerte werden dann in der Datenbank ohne weitere Konfiguration in als Zeichen folgen gespeichert `OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="c7105-163">Then the enum values will be saved as strings in the database without any further configuration in `OnModelCreating`.</span></span>

## <a name="limitations"></a><span data-ttu-id="c7105-164">Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="c7105-164">Limitations</span></span>

<span data-ttu-id="c7105-165">Es gibt einige bekannte aktuelle Einschränkungen des Value-Konvertierungs Systems:</span><span class="sxs-lookup"><span data-stu-id="c7105-165">There are a few known current limitations of the value conversion system:</span></span>

* <span data-ttu-id="c7105-166">Wie bereits erwähnt, `null` kann nicht konvertiert werden.</span><span class="sxs-lookup"><span data-stu-id="c7105-166">As noted above, `null` cannot be converted.</span></span>
* <span data-ttu-id="c7105-167">Zurzeit gibt es keine Möglichkeit, eine Konvertierung einer Eigenschaft in mehrere Spalten zu verteilen (oder umgekehrt).</span><span class="sxs-lookup"><span data-stu-id="c7105-167">There is currently no way to spread a conversion of one property to multiple columns or vice-versa.</span></span>
* <span data-ttu-id="c7105-168">Die Verwendung von Wert Konvertierungen kann sich auf die Fähigkeit von EF Core auswirken, Ausdrücke in SQL zu übersetzen.</span><span class="sxs-lookup"><span data-stu-id="c7105-168">Use of value conversions may impact the ability of EF Core to translate expressions to SQL.</span></span> <span data-ttu-id="c7105-169">In solchen Fällen wird eine Warnung protokolliert.</span><span class="sxs-lookup"><span data-stu-id="c7105-169">A warning will be logged for such cases.</span></span>
<span data-ttu-id="c7105-170">Das Entfernen dieser Einschränkungen wird für eine zukünftige Version in Erwägung gezogen.</span><span class="sxs-lookup"><span data-stu-id="c7105-170">Removal of these limitations is being considered for a future release.</span></span>
