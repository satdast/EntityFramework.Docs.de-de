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
# <a name="value-conversions"></a>Wertkonvertierungen

> [!NOTE]  
> Dieses Feature ist neu in EF Core 2.1.

Mithilfe von Wert Konvertern können Eigenschaftswerte beim Lesen aus der Datenbank oder beim Schreiben in die Datenbank konvertiert werden. Diese Konvertierung kann von einem Wert in einen anderen desselben Typs (z. b. das Verschlüsseln von Zeichen folgen) oder von einem Wert eines Typs zu einem Wert eines anderen Typs erfolgen (z. b. beim Konvertieren von Enumerationswerten in und aus Zeichen folgen in der Datenbank).

## <a name="fundamentals"></a>Grundlagen

Wert Konverter werden in Bezug auf einen `ModelClrType` und einen angegeben `ProviderClrType` . Der Modelltyp ist der .NET-Typ der-Eigenschaft im-Entitätstyp. Der Anbietertyp ist der .NET-Typ, der vom Datenbankanbieter verstanden wird. Um z. b. Enumerationen als Zeichen folgen in der Datenbank zu speichern, ist der Modelltyp der Typ der Enumeration, und der Anbietertyp ist `String` . Diese beiden Typen können identisch sein.

Konvertierungen werden mithilfe `Func` von zwei Ausdrucks Baumstrukturen definiert: einer von `ModelClrType` `ProviderClrType` und der andere von `ProviderClrType` bis `ModelClrType` . Ausdrucks Baumstrukturen werden verwendet, damit Sie für effiziente Konvertierungen in den Datenbankzugriffs Code kompiliert werden können. Bei komplexen Konvertierungen kann die Ausdrucks Baumstruktur ein einfacher aufrufungs Vorgang für eine Methode sein, die die Konvertierung ausführt.

## <a name="configuring-a-value-converter"></a>Konfigurieren eines Wert Konverters

Wert Konvertierungen werden für Eigenschaften in der definiert `OnModelCreating` `DbContext` . Stellen Sie sich z. b. eine Aufzählung und einen Entitätstyp vor

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

Anschließend können Konvertierungen in definiert werden `OnModelCreating` , um die Enumerationswerte als Zeichen folgen (z. b. "Esel", "maulzeichen",...) in der Datenbank zu speichern:

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
> Ein `null` Wert wird nie an einen Wert Konverter übermittelt. Dies vereinfacht die Implementierung von Konvertierungen und ermöglicht die gemeinsame Nutzung zwischen NULL-Werten und nicht auf NULL festleg baren Eigenschaften.

## <a name="the-valueconverter-class"></a>Die ValueConverter-Klasse

`HasConversion`Wenn Sie wie oben gezeigt aufrufen, wird eine `ValueConverter` -Instanz erstellt und für die-Eigenschaft festgelegt. `ValueConverter`Stattdessen kann explizit erstellt werden. Beispiel:

``` csharp
var converter = new ValueConverter<EquineBeast, string>(
    v => v.ToString(),
    v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```

Dies kann hilfreich sein, wenn mehrere Eigenschaften dieselbe Konvertierung verwenden.

> [!NOTE]  
> Es gibt derzeit keine Möglichkeit, an einem Ort anzugeben, dass jede Eigenschaft eines bestimmten Typs denselben Wert Konverter verwenden muss. Diese Funktion wird für eine zukünftige Version in Erwägung gezogen.

## <a name="built-in-converters"></a>Integrierte Konverter

EF Core enthält eine Reihe vordefinierter `ValueConverter` Klassen, die im- `Microsoft.EntityFrameworkCore.Storage.ValueConversion` Namespace enthalten sind. Dies sind:

* `BoolToZeroOneConverter` -Bool in NULL und eins
* `BoolToStringConverter` -Bool in Zeichen folgen wie "Y" und "N"
* `BoolToTwoValuesConverter` -Bool auf zwei beliebige Werte
* `BytesToStringConverter` -Bytearray in Base64-codierte Zeichenfolge
* `CastingConverter` -Konvertierungen, die nur eine Typumwandlung erfordern
* `CharToStringConverter` -Char in eine Zeichenfolge mit einem Zeichen
* `DateTimeOffsetToBinaryConverter` -DateTimeOffset in binary-codierter 64-Bit-Wert
* `DateTimeOffsetToBytesConverter` -DateTimeOffset in Bytearray
* `DateTimeOffsetToStringConverter` -DateTimeOffset in Zeichenfolge
* `DateTimeToBinaryConverter` -DateTime-to-64-Bit-Wert, einschließlich DateTimeKind
* `DateTimeToStringConverter` -DateTime zu String
* `DateTimeToTicksConverter` -DateTime zu Ticks
* `EnumToNumberConverter` -Enumeration zur zugrunde liegenden Zahl
* `EnumToStringConverter` -Enum in Zeichenfolge
* `GuidToBytesConverter` -GUID zu Bytearray
* `GuidToStringConverter` -GUID zu Zeichenfolge
* `NumberToBytesConverter` -Beliebiger numerischer Wert zum Bytearray
* `NumberToStringConverter` -Beliebiger numerischer Wert zu Zeichenfolge
* `StringToBytesConverter` -Zeichenfolge in UTF8-bytes
* `TimeSpanToStringConverter` -TimeSpan zu Zeichenfolge
* `TimeSpanToTicksConverter` -TimeSpan in Ticks

Beachten Sie, dass `EnumToStringConverter` in dieser Liste enthalten ist. Dies bedeutet, dass die Konvertierung nicht explizit angegeben werden muss, wie oben gezeigt. Verwenden Sie stattdessen einfach den integrierten Konverter:

``` csharp
var converter = new EnumToStringConverter<EquineBeast>();

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```

Beachten Sie, dass alle integrierten Konverter zustandslos sind, sodass eine einzelne Instanz auf sichere Weise von mehreren Eigenschaften gemeinsam genutzt werden kann.

## <a name="pre-defined-conversions"></a>Vordefinierte Konvertierungen

Für allgemeine Konvertierungen, bei denen ein integrierter Konverter vorhanden ist, muss der Konverter nicht explizit angegeben werden. Stattdessen konfigurieren Sie einfach den zu verwendenden Anbietertyp, und EF verwendet automatisch den entsprechenden integrierten Konverter. Konvertierungen von Enum zu Zeichen folgen werden als Beispiel oben verwendet, EF führt dies jedoch automatisch aus, wenn der Anbietertyp konfiguriert ist:

``` csharp
modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion<string>();
```

Dies kann auch durch explizites angeben des Spalten Typs erreicht werden. Beispielsweise, wenn der Entitätstyp wie folgt definiert ist:

``` csharp
public class Rider
{
    public int Id { get; set; }

    [Column(TypeName = "nvarchar(24)")]
    public EquineBeast Mount { get; set; }
}
```

Die Enumerationswerte werden dann in der Datenbank ohne weitere Konfiguration in als Zeichen folgen gespeichert `OnModelCreating` .

## <a name="limitations"></a>Einschränkungen

Es gibt einige bekannte aktuelle Einschränkungen des Value-Konvertierungs Systems:

* Wie bereits erwähnt, `null` kann nicht konvertiert werden.
* Zurzeit gibt es keine Möglichkeit, eine Konvertierung einer Eigenschaft in mehrere Spalten zu verteilen (oder umgekehrt).
* Die Verwendung von Wert Konvertierungen kann sich auf die Fähigkeit von EF Core auswirken, Ausdrücke in SQL zu übersetzen. In solchen Fällen wird eine Warnung protokolliert.
Das Entfernen dieser Einschränkungen wird für eine zukünftige Version in Erwägung gezogen.
