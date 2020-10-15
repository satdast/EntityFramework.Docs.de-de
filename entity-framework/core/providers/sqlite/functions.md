---
title: Funktions Zuordnungen-SQLite-Datenbankanbieter-EF Core
description: Funktions Zuordnungen des SQLite-EF Core Datenbankanbieters
author: bricelam
ms.date: 10/06/2020
uid: core/providers/sqlite/functions
ms.openlocfilehash: 0787981a0c6fa401a7ef4b4c3c2b406f78117ad1
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066529"
---
# <a name="function-mappings-of-the-sqlite-ef-core-provider"></a>Funktions Zuordnungen des SQLite-EF Core Anbieters

Diese Seite zeigt, welche .net-Member in welche SQL-Funktionen übersetzt werden, wenn der SQLite-Anbieter verwendet wird.

## <a name="binary-functions"></a>Binäre Funktionen

.NET                        | SQL                             | Hinzugefügt in
--------------------------- | ------------------------------- | --------
Satz. Enthält (Wert)       | InStr ( @bytes , char ( @value )) > 0 | EF Core 5.0
Satz. Füll                | Länge ( @bytes )                  | EF Core 5.0
Satz. SequenceEqual (Sekunde) | @bytes = @second                | EF Core 5.0

## <a name="date-and-time-functions"></a>Datums- und Uhrzeitfunktionen

.NET                            | SQL                                                                      | Hinzugefügt in
------------------------------- | ------------------------------------------------------------------------ | --------
DateTime.Now                    | DateTime (' Now ', ' localtime ')
DateTime. heute                  | DateTime ("Now", "localtime", "Start of Day")
DateTime.UtcNow                 | DateTime (' Now ')
DateTime. AddDays (Wert)         | DateTime ( @dateTime , @value \| \| ' Days ')
DateTime. AddHours (Wert)        | DateTime ( @dateTime , @d \| \| ' hours ')
DateTime. AddMilliseconds (Wert) | DateTime ( @dateTime , ( @value /1000,0) \| \| ' seconds ')                   | EF Core 2.2
DateTime. AddMinutes (Wert)      | DateTime ( @dateTime , @value \| \| ' minutes ')
DateTime. addmonate (Monate)      | DateTime ( @dateTime , @months \| \| ' Monate ')
DateTime. AddSeconds (Wert)      | DateTime ( @dateTime , @value \| \| ' seconds ')
DateTime. AddTicks (Wert)        | DateTime ( @dateTime , ( @value /10000000,0) \| \| ' seconds ')               | EF Core 2.2
DateTime. AddYears (Wert)        | DateTime ( @dateTime , @value \| \| ' years ')
DateTime. Date                   | DateTime ( @dateTime , ' Start of Day ')
DateTime. Day                    | Strauch Zeit ("% d", @dateTime )
DateTime. dayof-Woche              | Strauch Zeit ("% w", @dateTime )                                                | EF Core 2.2
DateTime. dayosyear              | Strauch Zeit ("% j", @dateTime )
DateTime. Hour                   | Strauch Zeit ("% H", @dateTime )
DateTime. Millisekunde            | (Strauch Zeit ("% f", @dateTime ) * 1000) %1000
DateTime. Minute                 | Strauch Zeit ("% M", @dateTime )
DateTime. Month                  | Strauch Zeit ("% m", @dateTime )
DateTime. Second                 | Strauch Zeit ("% S", @dateTime )
DateTime. Ticks                  | (julianday ( @dateTime )-julianday ("0001-01-01 00:00:00")) * 864 Milliarden | EF Core 2.2
DateTime. TimeOfDay              | Zeit ( @dateTime )                                                          | EF Core 3.0
DateTime. Year                   | Strauch Zeit ("% Y", @dateTime )

> [!NOTE]
> Einige SQL-Funktionen wurden zu Illustrations Zwecken vereinfacht. Die tatsächliche SQL-Verarbeitung ist komplexer, um eine größere Anzahl von Werten zu verarbeiten.

## <a name="numeric-functions"></a>Numerische Funktionen

.NET                  | SQL                                  | Hinzugefügt in
--------------------- | ------------------------------------ | --------
-DecimalValue         | ef_negate ( @decimalValue )             | EF Core 5.0
DecimalValue-d      | ef_add ( @decimalValue , ef_negate ( @d )) | EF Core 5.0
DecimalValue * d      | ef_multiply ( @decimalValue , @d )       | EF Core 5.0
DecimalValue/d      | ef_divide ( @decimalValue , @d )         | EF Core 5.0
DecimalValue% d      | ef_mod ( @decimalValue , @d )            | EF Core 5.0
DecimalValue + d      | ef_add ( @decimalValue , @d )            | EF Core 5.0
DecimalValue < d      | ef_compare ( @decimalValue , @d ) < 0    | EF Core 5.0
DecimalValue <= d     | ef_compare ( @decimalValue , @d ) <= 0   | EF Core 5.0
DecimalValue > d      | ef_compare ( @decimalValue , @d ) > 0    | EF Core 5.0
DecimalValue >= d     | ef_compare ( @decimalValue , @d ) >= 0   | EF Core 5.0
Double-Wert% d       | ef_mod ( @doubleValue , @d )             | EF Core 5.0
floatvalue% d        | ef_mod ( @floatValue , @d )              | EF Core 5.0
Math. Abs (Wert)       | Abs ( @value )
Math. Max (Wert1, Wert2)  | Max ( @val1 , @val2 )
Math. min (Wert1, Wert2)  | min ( @val1 , @val2 )
Math. Round (d)         | Round ( @d )
Math. Round (d, Ziffern) | Round ( @d , @digits )

> [!TIP]
> SQL-Funktionen, die *EF* als Präfix haben, werden EF Core erstellt.

## <a name="string-functions"></a>Zeichenfolgenfunktionen

.NET                                                         | SQL                                                    | Hinzugefügt in
------------------------------------------------------------ | ------------------------------------------------------ | --------
EF. Functions. COLLATE (Operand, COLLATIONS)                     | @operand COLLATE @collation                            | EF Core 5.0
EF. Functions. like (MatchExpression, Pattern)                  | @matchExpression mögen @pattern
EF. Functions. like (MatchExpression, pattern, escapecharacter) | @matchExpression LIKE- @pattern Escape @escapeCharacter
String. Compare (Stra, strB)                                   | Fall, wenn @strA = @strB Then 0... Schließlich
Schnür. Concat (str0, str1)                                    | @str0 \|\| @str1
Schnür. IsNullOrEmpty (Wert)                                  | @value ist NULL oder @value = ' '
Schnür. IsNullOrWhiteSpace (Wert)                             | @value Ist NULL oder Trim ( @value ) = ' '
StringValue. CompareTo (Straub)                                  | Fall, wenn @stringValue = @strB Then 0... Schließlich
StringValue. enthält (Wert)                                  | InStr ( @stringValue , @value ) > 0
StringValue. EndsWith (Wert)                                  | @stringValueLIKE "%" \| \|@value
StringValue. FirstOrDefault ()                                 | substr ( @stringValue , 1, 1)                             | EF Core 5.0
StringValue. IndexOf (Wert)                                   | InStr ( @stringValue , @value )-1
StringValue. LastOrDefault ()                                  | substr ( @stringValue , Länge ( @stringValue ), 1)          | EF Core 5.0
StringValue. length                                           | Länge ( @stringValue )
StringValue. Replace (oldValue, newValue)                      | Replace ( @stringValue , @oldValue , @newValue )
StringValue. starttwith (Wert)                                | @stringValueLIKE @value \| \| "%"
StringValue. Substring (startIndex, length)                    | substr ( @stringValue , @startIndex + 1, @length )
StringValue. ToLower ()                                        | niedriger ( @stringValue )
StringValue. ToUpper()                                        | Upper ( @stringValue )
StringValue. Trim ()                                           | Trim ( @stringValue )
StringValue. Trim (trimchar)                                   | Trim ( @stringValue , @trimChar )
StringValue. temend ()                                        | RTRIM ( @stringValue )
StringValue. TrimEnd (trimchar)                                | RTRIM ( @stringValue , @trimChar )
StringValue. TrimStart ()                                      | LTRIM ( @stringValue )
StringValue. TrimStart (trimchar)                              | LTRIM ( @stringValue , @trimChar )

> [!NOTE]
> Einige SQL-Funktionen wurden zu Illustrations Zwecken vereinfacht. Die tatsächliche SQL-Verarbeitung ist komplexer, um eine größere Anzahl von Werten zu verarbeiten.

## <a name="miscellaneous-functions"></a>Sonstige Funktionen

.NET                                     | SQL                                | Hinzugefügt in
---------------------------------------- | ---------------------------------- | --------
Ausstellung. Enthält (Element)                | @item IN @collection               | EF Core 3.0
EnumValue. hasflag (Flag)                  | @enumValue & @flag = @flag
Werte zulässt. GetValueOrDefault ()             | COALESCE ( @nullable , 0)
Werte zulässt. GetValueOrDefault (DefaultValue) | COALESCE ( @nullable , @defaultValue )

## <a name="see-also"></a>Weitere Informationen

* [Zuordnungen für räumliche Funktionen](xref:core/providers/sqlite/spatial#spatial-function-mappings)
