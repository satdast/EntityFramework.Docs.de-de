---
title: Funktions Zuordnungen-Microsoft SQL Server Datenbankanbieter-EF Core
description: Funktions Zuordnungen des Microsoft SQL Server Datenbankanbieters
author: bricelam
ms.date: 10/07/2020
uid: core/providers/sql-server/functions
ms.openlocfilehash: 8eb66521b00f2f4879a098200239849c7219a095
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066533"
---
# <a name="function-mappings-of-the-microsoft-sql-server-provider"></a>Funktions Zuordnungen des Microsoft SQL Server Anbieters

Diese Seite zeigt, welche .net-Member bei Verwendung des SQL Server Anbieters in welche SQL-Funktionen übersetzt werden.

## <a name="binary-functions"></a>Binäre Funktionen

.NET                         | SQL                           | Hinzugefügt in
---------------------------- | ----------------------------- | --------
Satz. Enthält (Wert)        | CHARINDEX ( @value , @bytes ) > 0 | EF Core 5.0
Satz. Füll                 | DATALENGTH ( @bytes )            | EF Core 5.0
Satz. SequenceEqual (Sekunde)  | @bytes = @second              | EF Core 5.0
EF. Functions. DATALENGTH (Arg) | DATALENGTH ( @arg )              | EF Core 5.0

## <a name="conversion-functions"></a>Konvertierungsfunktionen

.NET                      | SQL                                    | Hinzugefügt in
------------------------- | -------------------------------------- | --------
Satz. Zeichenfolge ()          | Konvertieren (varchar (100), @bytes )
ByteValue. Zeichenfolge ()      | Konvertieren (varchar (3), @byteValue )
charvalue.-Zeichenfolge ()      | Konvertieren (varchar (1), @charValue )
Konvertieren von "." (Wert)  | Konvertieren (Bit, @value )                   | EF Core 5.0
Convert. bybyte (Wert)     | Convert (tinyint, @value )
Convert. $ Decimal (Wert)  | Konvertieren (Decimal (18, 2), @value )
Konvertieren von "." (Wert)   | Konvertieren (float, @value )
Convert. ToInt16 (Wert)    | Konvertieren (smallint, @value )
Convert. ToInt32 (Wert)    | Konvertieren (int, @value )
Convert. ToInt64 (Wert)    | Konvertieren (bigint, @value )
Convert. $ String (Wert)   | Konvertieren (nvarchar (max), @value )
DateTime.-Zeichenfolge ()       | Konvertieren (varchar (100), @dateTime )
DateTimeOffset. ToString () | Konvertieren (varchar (100), @dateTimeOffset )
DecimalValue. destring ()   | Konvertieren (varchar (100), @decimalValue )
DoubleValue.-Zeichenfolge ()    | Konvertieren (varchar (100), @doubleValue )
floatvalue.-Zeichenfolge ()     | Konvertieren (varchar (100), @floatValue )
GUID. Zeichenfolge ()           | Konvertieren (varchar (36), @guid )
intvalue.-Zeichenfolge ()       | Konvertieren (varchar (11), @intValue )
longValue.-Zeichenfolge ()      | Konvertieren (varchar (20), @longValue )
sByteValue. destring ()     | Konvertieren (varchar (4), @sbyteValue )
shortValue.-Zeichenfolge ()     | Konvertieren (varchar (6), @shortValue )
TimeSpan.-Zeichenfolge ()       | Konvertieren (varchar (100), @timeSpan )
uintvalue.-Zeichenfolge ()      | Konvertieren (varchar (10), @uintValue )
uLongValue.-Zeichenfolge ()     | Konvertieren (varchar (19), @ulongValue )
uShortValue.-Zeichenfolge ()    | Konvertieren (varchar (5), @ushortValue )

## <a name="date-and-time-functions"></a>Datums- und Uhrzeitfunktionen

.NET                                                        | SQL                                                  | Hinzugefügt in
----------------------------------------------------------- | ---------------------------------------------------- | --------
DateTime.Now                                                | GETDATE ()
DateTime. heute                                              | Convert (Date, GETDATE ())
DateTime.UtcNow                                             | GETUTCDATE ()
DateTime. AddDays (Wert)                                     | DateAdd (Tag, @value , @dateTime )
DateTime. AddHours (Wert)                                    | DateAdd (Stunde, @value , @dateTime )
DateTime. AddMilliseconds (Wert)                             | DateAdd (Millisekunde, @value , @dateTime )
DateTime. AddMinutes (Wert)                                  | DateAdd (Minute, @value , @dateTime )
DateTime. addmonate (Monate)                                  | DateAdd (Monat, @months , @dateTime )
DateTime. AddSeconds (Wert)                                  | DateAdd (Second, @value , @dateTime )
DateTime. AddYears (Wert)                                    | DateAdd (Jahr, @value , @dateTime )
DateTime. Date                                               | Konvertieren (Datum, @dateTime )
DateTime. Day                                                | DatePart (Tag, @dateTime )
DateTime. dayosyear                                          | DatePart (dayosyear, @dateTime )
DateTime. Hour                                               | DatePart (Stunde, @dateTime )
DateTime. Millisekunde                                        | DatePart (Millisekunde, @dateTime )
DateTime. Minute                                             | DatePart (Minute, @dateTime )
DateTime. Month                                              | DatePart (Monat, @dateTime )
DateTime. Second                                             | DatePart (Sekunde, @dateTime )
DateTime. TimeOfDay                                          | Konvertieren (Zeit, @dateTime )                              | EF Core 2.2
DateTime. Year                                               | DatePart (Jahr, @dateTime )
DateTimeOffset.Now                                          | SYSDATETIMEOFFSET ()
DateTimeOffset. UtcNow                                       | SYSUTCDATETIME ()
DateTimeOffset. AddDays (Tage)                                | DateAdd (Tag, @days , @dateTimeOffset )
DateTimeOffset. AddHours (Stunden)                              | DateAdd (Stunde, @hours , @dateTimeOffset )
DateTimeOffset. AddMilliseconds (Millisekunden)                | DateAdd (Millisekunde, @milliseconds , @dateTimeOffset )
DateTimeOffset. AddMinutes (Minuten)                          | DateAdd (Minute, @minutes , @dateTimeOffset )
DateTimeOffset. addmonate (Monate)                            | DateAdd (Monat, @months , @dateTimeOffset )
DateTimeOffset. AddSeconds (Sekunden)                          | DateAdd (Second, @seconds , @dateTimeOffset )
DateTimeOffset. AddYears (Jahre)                              | DateAdd (Jahr, @years , @dateTimeOffset )
DateTimeOffset. Date                                         | Konvertieren (Datum, @dateTimeOffset )
DateTimeOffset. Day                                          | DatePart (Tag, @dateTimeOffset )
DateTimeOffset. dayosyear                                    | DatePart (dayosyear, @dateTimeOffset )
DateTimeOffset. Hour                                         | DatePart (Stunde, @dateTimeOffset )
DateTimeOffset. Millisekunde                                  | DatePart (Millisekunde, @dateTimeOffset )
DateTimeOffset. Minute                                       | DatePart (Minute, @dateTimeOffset )
DateTimeOffset. Month                                        | DatePart (Monat, @dateTimeOffset )
DateTimeOffset. Second                                       | DatePart (Sekunde, @dateTimeOffset )
DateTimeOffset. TimeOfDay                                    | Konvertieren (Zeit, @dateTimeOffset )                        | EF Core 2.2
DateTimeOffset. Year                                         | DatePart (Jahr, @dateTimeOffset )
EF. Functions. DateDiffDay (Start, Ende)                        | DateDiff (Tag, @start , @end )
EF. Functions. DateDiffHour (Start, Ende)                       | DateDiff (Stunde, @start , @end )
EF. Functions. datediffmikro Second (Start, Ende)                | DateDiff (Mikrosekunden, @start , @end )
EF. Functions. DateDiffMillisecond (Start, Ende)                | DateDiff (Millisekunde, @start , @end )
EF. Functions. DateDiffMinute (Start, Ende)                     | DateDiff (Minute, @start , @d2 )
EF. Functions. DateDiffMonth (Start, Ende)                      | DateDiff (Monat, @start , @end )
EF. Functions. DateDiffNanosecond (Start, Ende)                 | DateDiff (Nanosecond, @start , @end )
EF. Functions. DateDiffSecond (Start, Ende)                     | DateDiff (Sekunde, @start , @end )
EF. Functions. datediffweek (Start, Ende)                       | DateDiff (Woche, @start , @end )                         | EF Core 5.0
EF. Functions. DateDiffYear (Start, Ende)                       | DateDiff (Jahr, @start , @end )
EF. Functions. datefromparts (Jahr, Monat, Tag)                | Datefromparts ( @year , @month , @day )                   | EF Core 5.0
EF. Functions. DateTime2FromParts (Jahr, Monat, Tag,...)      | DATETIME2FROMPARTS ( @year , @month , @day ,...)         | EF Core 5.0
EF. Functions. datetimefromparts (Jahr, Monat, Tag,...)       | Datetimefromparts ( @year , @month , @day ,...)          | EF Core 5.0
EF. Functions. datetimeoffsetfromparts (Jahr, Monat, Tag,...) | Datetimeoffsetfromparts ( @year , @month , @day ,...)    | EF Core 5.0
EF. Functions. IsDate (Ausdruck)                             | IsDate ( @expression )                                  | EF Core 3.0
EF. Functions. smalldatetimefromparts (Jahr, Monat, Tag,...)  | Smalldatetimefromparts ( @year , @month , @day ,...)     | EF Core 5.0
EF. Functions. timefromparts (Stunde, Minute, Sekunde,...)       | Timefromparts ( @hour , @minute , @second ,...)          | EF Core 5.0
TimeSpan. Hours                                              | DatePart (Stunde, @timeSpan )                            | EF Core 5.0
TimeSpan. Millisekunden                                       | DatePart (Millisekunde, @timeSpan )                     | EF Core 5.0
TimeSpan. Minutes                                            | DatePart (Minute, @timeSpan )                          | EF Core 5.0
TimeSpan. seconds                                            | DatePart (Sekunde, @timeSpan )                          | EF Core 5.0

## <a name="numeric-functions"></a>Numerische Funktionen

.NET                    | SQL
----------------------- | ---
Math. Abs (Wert)         | Abs ( @value )
Math. acos (d)            | Acos ( @d )
Math. Asin (d)            | ASIN ( @d )
Math. Atan (d)            | Atan ( @d )
Math. atan2 (y, x)        | ATN2 ( @y , @x )
Math. Ceiling (d)         | Ceiling ( @d )
Math. cos (d)             | COS ( @d )
Math. Exp (d)             | Exp ( @d )
Math. Floor (d)           | Floor ( @d )
Math. log (d)             | Log ( @d )
Math. log (a, NEWBASE)    | Log ( @a , @newBase )
Math. log10 (d)           | Log10 ( @d )
Math. Pow (x, y)          | Power ( @x , @y )
Math. Round (d)           | Round ( @d , 0)
Math. Round (d, Dezimalstellen) | Round ( @d , @decimals )
Math. Sign (Wert)        | Sign ( @value )
Math. Sin (a)             | Sin ( @a )
Math. sqrt (d)            | SQRT ( @d )
Math. Tan (a)             | Tan ( @a )
Math. Truncate (d)        | Round ( @d , 0,0)

## <a name="string-functions"></a>Zeichenfolgenfunktionen

.NET                                                                    | SQL                                                                    | Hinzugefügt in
----------------------------------------------------------------------- | ---------------------------------------------------------------------- | --------
EF. Functions. COLLATE (Operand, COLLATIONS)                                | @operand COLLATE @collation                                            | EF Core 5.0
EF. Functions. enthält (PropertyReference, SearchCondition)               | Enthält ( @propertyReference , @searchCondition )                         | EF Core 2.2
EF. Functions. enthält (PropertyReference, SearchCondition, languageterm) | enthält ( @propertyReference , @searchCondition , Sprache @languageTerm ) | EF Core 2.2
EF. Functions. fretext (PropertyReference, fretext)                      | Freitext ( @propertyReference , @freeText )
EF. Functions. fretext (PropertyReference, fretext, languageterm)        | Freitext ( @propertyReference , @freeText , Sprache @languageTerm )
EF. Functions. like (MatchExpression, Pattern)                             | @matchExpression mögen @pattern
EF. Functions. like (MatchExpression, pattern, escapecharacter)            | @matchExpression LIKE- @pattern Escape @escapeCharacter
String. Compare (Stra, strB)                                              | Fall, wenn @strA = @strB Then 0... Schließlich
Schnür. Concat (str0, str1)                                               | @str0 + @str1
Schnür. IsNullOrEmpty (Wert)                                             | @value ist NULL oder @value = N ' '
Schnür. IsNullOrWhiteSpace (Wert)                                        | @value ist NULL oder LTrim (RTrim ( @value )) = N ' '
StringValue. CompareTo (Straub)                                             | Fall, wenn @stringValue = @strB Then 0... Schließlich
StringValue. enthält (Wert)                                             | @stringValue LIKE N '% ' + @value + N '% '
StringValue. EndsWith (Wert)                                             | @stringValue LIKE N '% ' + @value
StringValue. FirstOrDefault ()                                            | Teil Zeichenfolge ( @stringValue , 1, 1)                                          | EF Core 5.0
StringValue. IndexOf (Wert)                                              | CHARINDEX ( @value , @stringValue )-1
StringValue. LastOrDefault ()                                             | Teil Zeichenfolge ( @stringValue , Len ( @stringValue ), 1)                          | EF Core 5.0
StringValue. length                                                      | LEN ( @stringValue )
StringValue. Replace ( @oldValue , @newValue )                               | Replace ( @stringValue , @oldValue , @newValue )
StringValue. starttwith (Wert)                                           | @stringValue LIKE @value + N '% '
StringValue. Substring (startIndex, length)                               | Teil Zeichenfolge ( @stringValue , @startIndex + 1, @length )
StringValue. ToLower ()                                                   | Niedriger ( @stringValue )
StringValue. ToUpper()                                                   | Upper ( @stringValue )
StringValue. Trim ()                                                      | LTRIM (RTrim ( @stringValue ))
StringValue. temend ()                                                   | RTRIM ( @stringValue )
StringValue. TrimStart ()                                                 | LTRIM ( @stringValue )

## <a name="miscellaneous-functions"></a>Sonstige Funktionen

.NET                                     | SQL                                | Hinzugefügt in
---------------------------------------- | ---------------------------------- | --------
Ausstellung. Enthält (Element)                | @item IN @collection               | EF Core 3.0
EnumValue. hasflag (Flag)                  | @enumValue & @flag = @flag
Guid.NewGuid()                           | NEWID()
Werte zulässt. GetValueOrDefault ()             | COALESCE ( @nullable , 0)
Werte zulässt. GetValueOrDefault (DefaultValue) | COALESCE ( @nullable , @defaultValue )

> [!NOTE]
> Einige SQL-Funktionen wurden zu Illustrations Zwecken vereinfacht. Die tatsächliche SQL-Verarbeitung ist komplexer, um eine größere Anzahl von Werten zu verarbeiten.

## <a name="see-also"></a>Weitere Informationen

* [Zuordnungen für räumliche Funktionen](xref:core/providers/sql-server/spatial#spatial-function-mappings)
