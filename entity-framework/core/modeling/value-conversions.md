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
# <a name="value-conversions"></a>Wertkonvertierungen

Mithilfe von Wert Konvertern können Eigenschaftswerte beim Lesen aus der Datenbank oder beim Schreiben in die Datenbank konvertiert werden. Diese Konvertierung kann von einem Wert in einen anderen desselben Typs (z. b. das Verschlüsseln von Zeichen folgen) oder von einem Wert eines Typs zu einem Wert eines anderen Typs erfolgen (z. b. beim Konvertieren von Enumerationswerten in und aus Zeichen folgen in der Datenbank).

> [!TIP]
> Sie können den gesamten Code in dieser Dokumentation ausführen und debuggen, indem Sie [den Beispielcode von GitHub herunterladen](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/).

## <a name="overview"></a>Übersicht

Wert Konverter werden in Bezug auf einen `ModelClrType` und einen angegeben `ProviderClrType` . Der Modelltyp ist der .NET-Typ der-Eigenschaft im-Entitätstyp. Der Anbietertyp ist der .NET-Typ, der vom Datenbankanbieter verstanden wird. Um z. b. Enumerationen als Zeichen folgen in der Datenbank zu speichern, ist der Modelltyp der Typ der Enumeration, und der Anbietertyp ist `String` . Diese beiden Typen können identisch sein.

Konvertierungen werden mithilfe `Func` von zwei Ausdrucks Baumstrukturen definiert: einer von `ModelClrType` `ProviderClrType` und der andere von `ProviderClrType` bis `ModelClrType` . Ausdrucks Baumstrukturen werden verwendet, damit Sie für effiziente Konvertierungen in den Datenbankzugriffs Delegaten kompiliert werden können. Die Ausdrucks Baumstruktur kann einen einfachen aufrufungs Methode für komplexe Konvertierungen enthalten.

> [!NOTE]
> Eine Eigenschaft, die für die Wert Konvertierung konfiguriert wurde, muss möglicherweise auch ein angeben <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> . Weitere Informationen finden Sie in den Beispielen unten und in der Dokumentation zu den [Wert Comparer](xref:core/modeling/value-comparers) .

## <a name="configuring-a-value-converter"></a>Konfigurieren eines Wert Konverters

Wert Konvertierungen werden in konfiguriert <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType> . Stellen Sie sich z. b. eine Aufzählung und einen Entitätstyp vor

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

Konvertierungen können in so konfiguriert werden, <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> dass die Enumerationswerte als Zeichen folgen wie "Esel", "maulzeichen" usw. in der Datenbank gespeichert werden. Sie müssen lediglich eine Funktion bereitstellen, die aus dem in `ModelClrType` den konvertiert `ProviderClrType` und eine andere für die umgekehrte Konvertierung:

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
> Ein `null` Wert wird nie an einen Wert Konverter übermittelt. Ein NULL-Wert in einer Daten Bank Spalte ist in der Entitäts Instanz immer NULL (und umgekehrt). Dies vereinfacht die Implementierung von Konvertierungen und ermöglicht die gemeinsame Nutzung zwischen NULL-Werten und nicht auf NULL festleg baren Eigenschaften. Weitere Informationen finden Sie unter [GitHub-Problem #13850](https://github.com/dotnet/efcore/issues/13850) .

## <a name="pre-defined-conversions"></a>Vordefinierte Konvertierungen

EF Core enthält viele vordefinierte Konvertierungen, die das manuelle Schreiben von Konvertierungs Funktionen vermeiden. Stattdessen wählt EF Core die Konvertierung aus, die basierend auf dem Eigenschaftentyp im Modell und dem angeforderten Daten Bank Anbietertyp verwendet werden soll.

So werden z. b. Enumeration-zu-Zeichen folgen Konvertierungen als Beispiel verwendet, aber EF Core wird dies automatisch durchführen, wenn der Anbietertyp `string` mit dem generischen Typ von konfiguriert ist <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.HasConversion%2A> :

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

Dies kann auch durch explizites angeben des Daten Bank Spalten Typs erreicht werden. Beispielsweise, wenn der Entitätstyp wie folgt definiert ist:

### <a name="data-annotations"></a>[Daten Anmerkungen](#tab/data-annotations)

<!--
        public class Rider2
        {
            public int Id { get; set; }

            [Column(TypeName = "nvarchar(24)")]
            public EquineBeast Mount { get; set; }
        }
-->
[!code-csharp[ConversionByDatabaseType](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByDatabaseType)]

### <a name="fluent-api"></a>[Fluent-API](#tab/fluent-api)

<!--
                modelBuilder
                    .Entity<Rider2>()
                    .Property(e => e.Mount)
                    .HasColumnType("nvarchar(24)");
-->
[!code-csharp[ConversionByDatabaseTypeFluent](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByDatabaseTypeFluent)]

***

Die Enumerationswerte werden dann in der Datenbank ohne weitere Konfiguration in als Zeichen folgen gespeichert <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> .

## <a name="the-valueconverter-class"></a>Die ValueConverter-Klasse

<xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.HasConversion%2A>Wenn Sie wie oben gezeigt aufrufen, wird eine <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.ValueConverter%602> -Instanz erstellt und für die-Eigenschaft festgelegt. `ValueConverter`Stattdessen kann explizit erstellt werden. Beispiel:

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

Dies kann hilfreich sein, wenn mehrere Eigenschaften dieselbe Konvertierung verwenden.

## <a name="built-in-converters"></a>Integrierte Konverter

Wie bereits erwähnt, wird EF Core mit einem Satz vordefinierter Klassen ausgeliefert <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.ValueConverter%602> , die im- <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion> Namespace enthalten sind. In vielen Fällen wählt EF den passenden integrierten Konverter basierend auf dem Typ der Eigenschaft im Modell und dem in der Datenbank angeforderten Typ aus, wie oben für enumeraten gezeigt. Beispielsweise `.HasConversion<int>()` bewirkt die Verwendung von für eine Eigenschaft, dass `bool` EF Core boolesche Werte in numerische NULL und einen Wert konvertiert:

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

Dies ist funktional identisch mit dem Erstellen einer integrierten Instanz <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToZeroOneConverter%601> und der expliziten Festlegung:

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

In der folgenden Tabelle werden häufig verwendete vordefinierte Konvertierungen von Modell-/Eigenschaftentypen in Datenbankanbieter Typen zusammengefasst. In der Tabelle `any_numeric_type` ist einer von `int` , `short` , `long` , `byte` , `uint` , `ushort` , `ulong` , `sbyte` , `char` , `decimal` , `float` oder gemeint `double` .

| Modell-/Eigenschaftentyp | Anbieter/Datenbanktyp | Konvertierung                                                | Verbrauch
|:--------------------|------------------------|-----------------------------------------------------------|------
| bool                | any_numeric_type       | False/true für 0/1                                         | `.HasConversion<any_numeric_type>()`
|                     | any_numeric_type       | False/true für beliebige zwei Zahlen                             | Verwenden Sie <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToTwoValuesConverter%601>
|                     | Zeichenfolge                 | False/true für "Y"/"N"                                     | `.HasConversion<string>()`
|                     | Zeichenfolge                 | False/true für beliebige zwei Zeichen folgen                             | Verwenden Sie <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToStringConverter>
| any_numeric_type    | bool                   | 0/1 zu false/true                                         | `.HasConversion<bool>()`
|                     | any_numeric_type       | Einfache Umwandlung                                               | `.HasConversion<any_numeric_type>()`
|                     | Zeichenfolge                 | Die Zahl als Zeichenfolge.                                    | `.HasConversion<string>()`
| Enumeration                | any_numeric_type       | Der numerische Wert der Enumeration.                             | `.HasConversion<any_numeric_type>()`
|                     | Zeichenfolge                 | Die Zeichen folgen Darstellung des Enumerationswerts.               | `.HasConversion<string>()`
| Zeichenfolge              | bool                   | Analysiert die Zeichenfolge als bool.                               | `.HasConversion<bool>()`
|                     | any_numeric_type       | Analysiert die Zeichenfolge als den angegebenen numerischen Typ.               | `.HasConversion<any_numeric_type>()`
|                     | char                   | Das erste Zeichen der Zeichenfolge.                         | `.HasConversion<char>()`
|                     | Datetime               | Analysiert die Zeichenfolge als DateTime-Wert.                           | `.HasConversion<DateTime>()`
|                     | DateTimeOffset         | Analysiert die Zeichenfolge als DateTimeOffset.                     | `.HasConversion<DateTimeOffset>()`
|                     | TimeSpan               | Analysiert die Zeichenfolge als TimeSpan.                           | `.HasConversion<TimeSpan>()`
|                     | Guid                   | Analysiert die Zeichenfolge als GUID.                               | `.HasConversion<Guid>()`
|                     | byte[]                 | Die Zeichenfolge als UTF8-bytes.                                  | `.HasConversion<byte[]>()`
| char                | Zeichenfolge                 | Eine Zeichenfolge mit einem einzelnen Zeichen                                 | `.HasConversion<string>()`
| Datetime            | long                   | Codiertes Datum/Uhrzeit-Beibehaltung von DateTime. Kind                | `.HasConversion<long>()`
|                     | long                   | Tickt                                                     | Verwenden Sie <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToTicksConverter>
|                     | Zeichenfolge                 | Datums-/Uhrzeitzeichenfolge der invarianten Kultur                        | `.HasConversion<string>()`
| DateTimeOffset      | long                   | Codiertes Datum/Uhrzeit mit Offset                             | `.HasConversion<long>()`
|                     | Zeichenfolge                 | Datum/Uhrzeit-Zeichenfolge der invarianten Kultur mit Offset            | `.HasConversion<string>()`
| TimeSpan            | long                   | Tickt                                                     | `.HasConversion<long>()`
|                     | Zeichenfolge                 | Zeichenfolge für die invariante Kulturzeit Spanne                        | `.HasConversion<string>()`
| Uri                 | Zeichenfolge                 | Der URI als Zeichenfolge.                                       | `.HasConversion<string>()`
| PhysicalAddress     | Zeichenfolge                 | Die Adresse als Zeichenfolge.                                   | `.HasConversion<string>()`
|                     | byte[]                 | Bytes in Big-d-Netzwerk Reihenfolge                         | `.HasConversion<byte[]>()`
| IPAddress           | Zeichenfolge                 | Die Adresse als Zeichenfolge.                                   | `.HasConversion<string>()`
|                     | byte[]                 | Bytes in Big-d-Netzwerk Reihenfolge                         | `.HasConversion<byte[]>()`
| Guid                | Zeichenfolge                 | Der GUID im auf: dddddddd-dddd-dddd-dddd-dddddddddddd-Format. | `.HasConversion<string>()`
|                     | byte[]                 | Bytes in der binären Serialisierungsreihenfolge von .net                  | `.HasConversion<byte[]>()`

Beachten Sie, dass diese Konvertierungen davon ausgehen, dass das Format des Werts für die Konvertierung geeignet ist. Wenn z. b. Zeichen folgen in Zahlen konvertiert werden, treten Fehler auf, wenn die Zeichen folgen Werte nicht als Zahlen analysiert werden können.

Die vollständige Liste der integrierten Konverter lautet:

* Umstellen von booleschen Eigenschaften:
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToStringConverter> -Bool in Zeichen folgen wie "Y" und "N"
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToTwoValuesConverter%601> -Bool auf zwei beliebige Werte
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToZeroOneConverter%601> -Bool in NULL und eins
* Bytearray-Eigenschaften werden umgerechnet:
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BytesToStringConverter> -Bytearray in Base64-codierte Zeichenfolge
* Jede Konvertierung, die nur eine Typumwandlung erfordert
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.CastingConverter%602> -Konvertierungen, die nur eine Typumwandlung erfordern
* Char-Eigenschaften werden umgerechnet:
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.CharToStringConverter> -Char in eine Zeichenfolge mit einem Zeichen
* Eigenschaften werden umgerechnet <xref:System.DateTimeOffset> :
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeOffsetToBinaryConverter> - <xref:System.DateTimeOffset> in binär codierten 64-Bit-Wert
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeOffsetToBytesConverter> - <xref:System.DateTimeOffset> in Bytearray
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeOffsetToStringConverter> - <xref:System.DateTimeOffset> in Zeichenfolge
* Eigenschaften werden umgerechnet <xref:System.DateTime> :
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToBinaryConverter> - <xref:System.DateTime> auf 64-Bit-Wert einschließlich DateTimeKind
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToStringConverter> - <xref:System.DateTime> in Zeichenfolge
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToTicksConverter> - <xref:System.DateTime> in Ticks
* Eigenschaften von Enumeration werden umgerechnet:
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.EnumToNumberConverter%602> -Enumeration zur zugrunde liegenden Zahl
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.EnumToStringConverter%601> -Enum in Zeichenfolge
* Eigenschaften werden umgerechnet <xref:System.Guid> :
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.GuidToBytesConverter> - <xref:System.Guid> in Bytearray
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.GuidToStringConverter> - <xref:System.Guid> in Zeichenfolge
* Eigenschaften werden umgerechnet <xref:System.Net.IPAddress> :
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.IPAddressToBytesConverter> - <xref:System.Net.IPAddress> in Bytearray
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.IPAddressToStringConverter> - <xref:System.Net.IPAddress> in Zeichenfolge
* Umrechnen von numerischen Eigenschaften (int, Double, Decimal usw.):
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.NumberToBytesConverter%601> -Beliebiger numerischer Wert zum Bytearray
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.NumberToStringConverter%601> -Beliebiger numerischer Wert zu Zeichenfolge
* Eigenschaften werden umgerechnet <xref:System.Net.NetworkInformation.PhysicalAddress> :
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.PhysicalAddressToBytesConverter> - <xref:System.Net.NetworkInformation.PhysicalAddress> in Bytearray
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.PhysicalAddressToStringConverter> - <xref:System.Net.NetworkInformation.PhysicalAddress> in Zeichenfolge
* Zeichen folgen Eigenschaften werden umgerechnet:
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToBoolConverter> -Zeichen folgen wie "Y" und "N" in "bool"
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToBytesConverter> -Zeichenfolge in UTF8-bytes
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToCharConverter> -Zeichenfolge in Zeichen
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToDateTimeConverter> -Zeichenfolge in <xref:System.DateTime>
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToDateTimeOffsetConverter> -Zeichenfolge in <xref:System.DateTimeOffset>
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToEnumConverter%601> -Zeichenfolge in Enumeration
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToGuidConverter> -Zeichenfolge in <xref:System.Guid>
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToNumberConverter%601> -Zeichenfolge in numerischen Typ
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToTimeSpanConverter> -Zeichenfolge in <xref:System.TimeSpan>
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToUriConverter> -Zeichenfolge in <xref:System.Uri>
* Eigenschaften werden umgerechnet <xref:System.TimeSpan> :
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.TimeSpanToStringConverter> - <xref:System.TimeSpan> in Zeichenfolge
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.TimeSpanToTicksConverter> - <xref:System.TimeSpan> in Ticks
* Eigenschaften werden umgerechnet <xref:System.Uri> :
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.UriToStringConverter> - <xref:System.Uri> in Zeichenfolge

Beachten Sie, dass alle integrierten Konverter zustandslos sind, sodass eine einzelne Instanz auf sichere Weise von mehreren Eigenschaften gemeinsam genutzt werden kann.

## <a name="column-facets-and-mapping-hints"></a>Spalten Facetten und Mapping-Hinweise

Einige Datenbanktypen verfügen über Facetten, die die Speicherung der Daten ändern. Dazu gehören:

* Genauigkeit und Dezimalstellen für Dezimalzahlen und Datums-/Uhrzeitspalten
* Größe/Länge für binäre Spalten und Zeichen folgen Spalten
* Unicode für Zeichen folgen Spalten

Diese Facetten können für eine Eigenschaft, die einen Wert Konverter verwendet, auf normale Weise konfiguriert werden und gelten für den konvertierten Datenbanktyp. Wenn Sie z. b. von einer Enumeration in Zeichen folgen umrechnen, können wir angeben, dass die Daten Bank Spalte nicht-Unicode sein soll und bis zu 20 Zeichen speichern soll:

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

Oder, wenn der Konverter explizit erstellt wird:

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

Dies führt zu einer `varchar(20)` Spalte, wenn EF Core Migrationen für SQL Server verwendet werden:

```sql
CREATE TABLE [Rider] (
    [Id] int NOT NULL IDENTITY,
    [Mount] varchar(20) NOT NULL,
    CONSTRAINT [PK_Rider] PRIMARY KEY ([Id]));
```

Wenn allerdings standardmäßig alle `EquineBeast` Spalten lauten sollen `varchar(20)` , können diese Informationen dem Wert Konverter als übergeben werden <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.ConverterMappingHints> . Beispiel:

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

Bei jeder Verwendung dieses Konverters ist die Daten Bank Spalte nicht-Unicode mit einer maximalen Länge von 20. Dies sind jedoch nur Hinweise, da Sie von allen Facetten überschrieben werden, die explizit für die zugeordnete Eigenschaft festgelegt wurden.

## <a name="examples"></a>Beispiele

### <a name="simple-value-objects"></a>Einfache Wert Objekte

In diesem Beispiel wird ein einfacher Typ verwendet, um einen primitiven Typ zu wrappen. Dies kann hilfreich sein, wenn Sie möchten, dass der Typ im Modell spezifischer (und somit typsicherer) als ein primitiver Typ ist. In diesem Beispiel ist `Dollars` der Typ, der den dezimalen primitiven umschließt:

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

Dies kann in einem Entitätstyp verwendet werden:

<!--
        public class Order
        {
            public int Id { get; set; }

            public Dollars Price { get; set; }
        }
-->
[!code-csharp[SimpleValueObjectModel](../../../samples/core/Modeling/ValueConversions/SimpleValueObject.cs?name=SimpleValueObjectModel)]

Und in die zugrunde liegende konvertiert, `decimal` Wenn Sie in der Datenbank gespeichert werden:

<!--
                modelBuilder.Entity<Order>()
                    .Property(e => e.Price)
                    .HasConversion(
                        v => v.Amount,
                        v => new Dollars(v));
-->
[!code-csharp[ConfigureImmutableStructProperty](../../../samples/core/Modeling/ValueConversions/SimpleValueObject.cs?name=ConfigureImmutableStructProperty)]

> [!NOTE]
> Dieses Wertobjekt wird als schreibgeschützte [Struktur](/dotnet/csharp/language-reference/builtin-types/struct)implementiert. Dies bedeutet, dass EF Core Werte ohne Probleme erstellen und vergleichen können. Weitere Informationen finden Sie unter [Wert Vergleiche](xref:core/modeling/value-comparers) .

### <a name="composite-value-objects"></a>Zusammengesetzte Wert Objekte

Im vorherigen Beispiel enthielt der Wert Objekttyp nur eine einzelne Eigenschaft. Es kommt häufiger vor, dass ein Wert Objekttyp mehrere Eigenschaften zusammenfasst, die zusammen ein Domänen Konzept bilden. Beispielsweise ein allgemeiner `Money` Typ, der sowohl den Betrag als auch die Währung enthält:

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

Dieses Wertobjekt kann in einem Entitätstyp wie zuvor verwendet werden:

<!--
        public class Order
        {
            public int Id { get; set; }

            public Money Price { get; set; }
        }
-->
[!code-csharp[CompositeValueObjectModel](../../../samples/core/Modeling/ValueConversions/CompositeValueObject.cs?name=CompositeValueObjectModel)]

Wert Konverter können derzeit nur Werte in eine und aus einer einzelnen Daten Bank Spalte konvertieren. Diese Einschränkung bedeutet, dass alle Eigenschaftswerte aus dem Objekt in einen einzelnen Spaltenwert codiert werden müssen. Dies wird in der Regel durch Serialisieren des Objekts in die Datenbank und anschließendes Deserialisieren des Objekts behandelt. Verwenden Sie beispielsweise Folgendes <xref:System.Text.Json> :

<!--
                modelBuilder.Entity<Order>()
                    .Property(e => e.Price)
                    .HasConversion(
                        v => JsonSerializer.Serialize(v, null),
                        v => JsonSerializer.Deserialize<Money>(v, null));
-->
[!code-csharp[ConfigureCompositeValueObject](../../../samples/core/Modeling/ValueConversions/CompositeValueObject.cs?name=ConfigureCompositeValueObject)]

> [!NOTE]
> Wir planen, das Mapping eines Objekts mehreren Spalten in EF Core 6,0 zu ermöglichen. Hierdurch entfällt die Notwendigkeit, die Serialisierung zu verwenden. Dies wird durch das [GitHub-Problem #13947](https://github.com/dotnet/efcore/issues/13947)nachverfolgt.

> [!NOTE]
> Wie im vorherigen Beispiel wird dieses Wertobjekt als schreibgeschützte [Struktur](/dotnet/csharp/language-reference/builtin-types/struct)implementiert. Dies bedeutet, dass EF Core Werte ohne Probleme erstellen und vergleichen können. Weitere Informationen finden Sie unter [Wert Vergleiche](xref:core/modeling/value-comparers) .

### <a name="collections-of-primitives"></a>Auflistungen von primitiven

Die Serialisierung kann auch zum Speichern einer Auflistung primitiver Werte verwendet werden. Beispiel:

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

Erneutes verwenden <xref:System.Text.Json> :

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

`ICollection<string>` stellt einen änderbaren Referenztyp dar. Dies bedeutet, dass eine <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> erforderlich ist, damit EF Core Änderungen ordnungsgemäß verfolgen und erkennen können. Weitere Informationen finden Sie unter [Wert Vergleiche](xref:core/modeling/value-comparers) .

### <a name="collections-of-value-objects"></a>Auflistungen von Wert Objekten

Wenn Sie die beiden vorherigen Beispiele zusammen kombinieren, können wir eine Auflistung von Wert Objekten erstellen. Stellen Sie sich beispielsweise einen Typ vor, `AnnualFinance` der die Blog Finanzen für ein einzelnes Jahr modelliert:

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

Dieser Typ verfasst einige der Typen, die `Money` wir zuvor erstellt haben:

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

Anschließend können wir dem Entitätstyp eine Auflistung von hinzufügen `AnnualFinance` :

<!--
        public class Blog
        {
            public int Id { get; set; }
            public string Name { get; set; }
            
            public IList<AnnualFinance> Finances { get; set; }
        }
-->
[!code-csharp[ValueObjectCollectionModel](../../../samples/core/Modeling/ValueConversions/ValueObjectCollection.cs?name=ValueObjectCollectionModel)]

Verwenden Sie die Serialisierung auch, um Folgendes zu speichern:

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
> Wie zuvor ist für diese Konvertierung eine erforderlich <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> . Weitere Informationen finden Sie unter [Wert Vergleiche](xref:core/modeling/value-comparers) .

### <a name="value-objects-as-keys"></a>Wert Objekte als Schlüssel

Manchmal können primitive Schlüsseleigenschaften in Wert Objekte umschließt werden, um beim Zuweisen von Werten eine zusätzliche Ebene der Typsicherheit hinzuzufügen. Wir könnten beispielsweise einen Schlüsseltyp für Blogs und einen Schlüsseltyp für Beiträge implementieren:

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

Diese können dann im Domänen Modell verwendet werden:

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

Beachten Sie, dass `Blog.Id` nicht versehentlich eine zugewiesen werden kann `PostKey` und `Post.Id` nicht versehentlich ein zugewiesen werden kann `BlogKey` . Ebenso muss der `Post.BlogId` Fremdschlüssel Eigenschaft ein zugewiesen werden `BlogKey` .

> [!NOTE]
> Wenn dieses Muster angezeigt wird, bedeutet dies nicht, dass es empfohlen wird. Überlegen Sie, ob diese Abstraktions Ebene das Entwicklungsverfahren unterstützt oder behindert. Sie sollten auch die Verwendung von Navigationen und generierten Schlüsseln in Erwägung gezogen, anstatt direkt mit Schlüsselwerten umzugehen.

Diese Schlüsseleigenschaften können dann mithilfe von Wert Konvertern zugeordnet werden:

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
> Für Schlüsseleigenschaften mit Konvertierungen können derzeit keine generierten Schlüsselwerte verwendet werden. Stimmen Sie für das [GitHub-Problem #11597](https://github.com/dotnet/efcore/issues/11597) , dass diese Einschränkung entfernt werden soll.

### <a name="use-ulong-for-timestamprowversion"></a>Verwenden von ULONG für Timestamp/rowversion

SQL Server unterstützt [die automatische voll](xref:core/saving/concurrency) ständige Parallelität mithilfe von [8-Byte-Binär `rowversion` / `timestamp` Spalten](/sql/t-sql/data-types/rowversion-transact-sql). Diese werden stets mithilfe eines 8-Byte-Arrays aus der Datenbank gelesen und in diese geschrieben. Byte Arrays sind jedoch ein änderbarer Verweistyp, der die Handhabung einigermaßen erschwert. Mithilfe von Wert Konvertern kann `rowversion` stattdessen eine Eigenschaft zugeordnet `ulong` werden, die wesentlich besser geeignet und leichter zu verwenden ist als das Bytearray. Stellen Sie sich z. b. eine `Blog` Entität mit einem ULONG-Parallelitäts Token vor:

<!--
        public class Blog
        {
            public int Id { get; set; }
            public string Name { get; set; }
            public ulong Version { get; set; }
        }
-->
[!code-csharp[ULongConcurrencyModel](../../../samples/core/Modeling/ValueConversions/ULongConcurrency.cs?name=ULongConcurrencyModel)]

Dies kann `rowversion` mit einem Wert Konverter einer SQL Server-Spalte zugeordnet werden:

<!--
                modelBuilder.Entity<Blog>()
                    .Property(e => e.Version)
                    .IsRowVersion()
                    .HasConversion<byte[]>();
-->
[!code-csharp[ConfigureULongConcurrency](../../../samples/core/Modeling/ValueConversions/ULongConcurrency.cs?name=ConfigureULongConcurrency)]

### <a name="specify-the-datetimekind-when-reading-dates"></a>DateTime. Kind beim Lesen von Datumsangaben angeben

SQL Server verwirft das- <xref:System.DateTime.Kind%2A?displayProperty=nameWithType> Flag, wenn ein <xref:System.DateTime> als oder gespeichert wird [`datetime`](/sql/t-sql/data-types/datetime-transact-sql) [`datetime2`](/sql/t-sql/data-types/datetime2-transact-sql) . Dies bedeutet, dass DateTime-Werte, die von der Datenbank zurückkommen, immer eine <xref:System.DateTimeKind> von aufweisen `Unspecified` .

Wert Konverter können auf zwei Arten verwendet werden, um damit umzugehen. Zuerst verfügt EF Core über einen Wert Konverter, der einen nicht transparenten 8-Byte-Wert erstellt, der das-Flag beibehält `Kind` . Beispiel:

<!--
                modelBuilder.Entity<Post>()
                    .Property(e => e.PostedOn)
                    .HasConversion<long>();
-->
[!code-csharp[ConfigurePreserveDateTimeKind1](../../../samples/core/Modeling/ValueConversions/PreserveDateTimeKind.cs?name=ConfigurePreserveDateTimeKind1)]

Dadurch können DateTime-Werte mit unterschiedlichen `Kind` Flags in der Datenbank gemischt werden.

Das Problem bei diesem Ansatz besteht darin, dass die Datenbank nicht mehr über erkennbare- `datetime` oder- `datetime2` Spalten verfügt. Stattdessen ist es üblich, immer die UTC-Zeit (oder seltener, immer Ortszeit) zu speichern und dann entweder das `Kind` Flag zu ignorieren oder es mit einem Wert Konverter auf den entsprechenden Wert festzulegen. Beispielsweise stellt der Konverter unten sicher, dass der `DateTime` aus der Datenbank gelesene Wert Folgendes hat <xref:System.DateTimeKind> `UTC` :

<!--
                modelBuilder.Entity<Post>()
                    .Property(e => e.LastUpdated)
                    .HasConversion(
                        v => v,
                        v => new DateTime(v.Ticks, DateTimeKind.Utc));
-->
[!code-csharp[ConfigurePreserveDateTimeKind2](../../../samples/core/Modeling/ValueConversions/PreserveDateTimeKind.cs?name=ConfigurePreserveDateTimeKind2)]

Wenn eine Mischung aus lokalen und UTC-Werten in Entitäts Instanzen festgelegt wird, kann der Konverter verwendet werden, um vor dem Einfügen entsprechend zu konvertieren. Beispiel:

<!--
                modelBuilder.Entity<Post>()
                    .Property(e => e.LastUpdated)
                    .HasConversion(
                        v => v.ToUniversalTime(),
                        v => new DateTime(v.Ticks, DateTimeKind.Utc));
-->
[!code-csharp[ConfigurePreserveDateTimeKind3](../../../samples/core/Modeling/ValueConversions/PreserveDateTimeKind.cs?name=ConfigurePreserveDateTimeKind3)]

> [!NOTE]
> Achten Sie sorgfältig darauf, den gesamten Datenbankzugriffs Code so zu vereinheitlichen, dass die UTC-Zeit immer verwendet wird

### <a name="use-case-insensitive-string-keys"></a>Zeichen folgen Schlüssel mit Groß-/Kleinschreibung verwenden

Einige Datenbanken, einschließlich SQL Server, führen standardmäßig Zeichen folgen Vergleiche ohne Berücksichtigung der Groß-und Kleinschreibung durch .NET hingegen führt standardmäßig die Groß-/Kleinschreibung für Zeichen folgen Vergleiche durch. Dies bedeutet, dass ein Fremdschlüssel Wert wie "dotnet" mit dem Primärschlüssel Wert "dotnet" auf SQL Server, aber nicht mit dem Wert in EF Core abgeglichen wird. Ein Wert Vergleich für Schlüssel kann verwendet werden, um zu erzwingen, dass EF Core groß-und Kleinschreibung für Zeichen folgen Vergleiche wie in der Datenbank nicht beachtet. Stellen Sie sich beispielsweise ein Blog-/Post-Modell mit Zeichen folgen Schlüsseln vor:

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

Dies funktioniert nicht wie erwartet, wenn einige der Werte eine andere Schreibweise `Post.BlogId` aufweisen. Die Fehler, die dadurch verursacht werden, hängen davon ab, wie die Anwendung ausgeführt wird, aber in der Regel Diagramme von Objekten, die nicht ordnungsgemäß korrigiert werden, und/oder Updates, die fehlschlagen, weil der FK [-](xref:core/change-tracking/relationship-changes) Wert falsch ist. Ein Wert Vergleich kann verwendet werden, um Folgendes zu beheben:

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
> .Net-Zeichen folgen Vergleiche und Daten Bank Zeichen folgen Vergleiche können sich in mehr als nur der Groß-/Kleinschreibung Dieses Muster funktioniert für einfache ASCII-Schlüssel, kann jedoch für Schlüssel mit beliebigen kulturspezifischen Zeichen fehlschlagen. Weitere Informationen finden Sie unter [Sortierungen und Groß-/Kleinschreibung](xref:core/miscellaneous/collations-and-case-sensitivity) .

### <a name="handle-fixed-length-database-strings"></a>Verarbeiten von Daten Bank Zeichenfolgen fester Länge

Im vorherigen Beispiel wurde kein Wert Konverter benötigt. Ein Konverter kann jedoch für Daten Bank Zeichen folgen Typen mit fester Länge wie oder nützlich sein `char(20)` `nchar(20)` . Zeichen folgen mit fester Länge werden in der vollständigen Länge aufgefüllt, wenn ein Wert in die Datenbank eingefügt wird. Dies bedeutet, dass der Schlüsselwert " `dotnet` " aus der Datenbank als "" gelesen wird `dotnet..............` , wobei `.` ein Leerzeichen darstellt. Dies wird dann nicht ordnungsgemäß mit Schlüsselwerten verglichen, die nicht aufgefüllt werden.

Ein Wert Konverter kann verwendet werden, um die Auffüll Zeichen beim Lesen von Schlüsselwerten zu kürzen. Dies kann im vorherigen Beispiel mit dem Wert Vergleich kombiniert werden, um die Groß-/Kleinschreibung ohne Berücksichtigung der Groß-/Kleinschreibung zu vergleichen. Beispiel:

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

### <a name="encrypt-property-values"></a>Verschlüsseln von Eigenschafts Werten

Wert Konverter können verwendet werden, um Eigenschaftswerte vor dem Senden an die Datenbank zu verschlüsseln und Sie dann auf dem Weg zu entschlüsseln. Verwenden Sie z. b. die Zeichen folgen Umkehrung als Ersatz für einen echten Verschlüsselungsalgorithmus:

<!--
                modelBuilder.Entity<User>().Property(e => e.Password).HasConversion(
                    v => new string(v.Reverse().ToArray()),
                    v => new string(v.Reverse().ToArray()));
-->
[!code-csharp[ConfigureEncryptPropertyValues](../../../samples/core/Modeling/ValueConversions/EncryptPropertyValues.cs?name=ConfigureEncryptPropertyValues)]

> [!NOTE]
> Es gibt derzeit keine Möglichkeit, einen Verweis auf den aktuellen dbcontext oder einen anderen Sitzungszustand innerhalb eines Wert Konverters zu erhalten. Dies schränkt die Arten der Verschlüsselung ein, die verwendet werden können. Stimmen Sie für das [GitHub-Problem #11597](https://github.com/dotnet/efcore/issues/12205) , dass diese Einschränkung entfernt werden soll.

> [!WARNING]
> Stellen Sie sicher, dass Sie alle Implikationen verstehen, wenn Sie eine eigene Verschlüsselung zum Schutz sensibler Daten ausführen. Verwenden Sie stattdessen vorgefertigte Verschlüsselungsmechanismen, z. b. [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) auf SQL Server.

## <a name="limitations"></a>Einschränkungen

Es gibt einige bekannte aktuelle Einschränkungen des Value-Konvertierungs Systems:

* Es gibt derzeit keine Möglichkeit, an einem Ort anzugeben, dass jede Eigenschaft eines bestimmten Typs denselben Wert Konverter verwenden muss. Bitte stimmen 👍 Sie () auf [GitHub-Problem #10784](https://github.com/dotnet/efcore/issues/10784) ab, wenn dies etwas ist, das Sie benötigen.
* Wie bereits erwähnt, `null` kann nicht konvertiert werden. Bitte stimmen 👍 Sie () auf [GitHub-Problem #13850](https://github.com/dotnet/efcore/issues/13850) ab, wenn dies etwas ist, das Sie benötigen.
* Zurzeit gibt es keine Möglichkeit, eine Konvertierung einer Eigenschaft in mehrere Spalten zu verteilen (oder umgekehrt). Bitte stimmen 👍 Sie () auf [GitHub-Problem #13947](https://github.com/dotnet/efcore/issues/13947) ab, wenn dies etwas ist, das Sie benötigen.
* Die Generierung von Werten wird für die meisten durch Wert Konverter zugeordneten Schlüssel nicht unterstützt. Bitte stimmen 👍 Sie () auf [GitHub-Problem #11597](https://github.com/dotnet/efcore/issues/11597) ab, wenn dies etwas ist, das Sie benötigen.
* Wert Konvertierungen können nicht auf die aktuelle dbcontext-Instanz verweisen. Bitte stimmen 👍 Sie () auf [GitHub-Problem #11597](https://github.com/dotnet/efcore/issues/12205) ab, wenn dies etwas ist, das Sie benötigen.

Das Entfernen dieser Einschränkungen wird in zukünftigen Versionen berücksichtigt.
