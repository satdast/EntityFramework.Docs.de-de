---
title: Arbeiten mit Verweis Typen, die NULL-Werte zulassen-EF Core
description: Arbeiten mit Verweis Typen, die NULL-Werte zulassen, bei Verwendung von Entity Framework Core
author: roji
ms.date: 09/09/2019
uid: core/miscellaneous/nullable-reference-types
ms.openlocfilehash: 648b79576838d2ba424b5216d5ad6811912f8ccb
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429714"
---
# <a name="working-with-nullable-reference-types"></a>Arbeiten mit Verweis Typen, die NULL-Werte zulassen

In c# 8 wurde ein neues Feature namens " [Werte zulässt Reference Types (NRT)](/dotnet/csharp/tutorials/nullable-reference-types)" eingeführt, sodass Verweis Typen mit Anmerkungen versehen werden können. Dies gibt an, ob es zulässig ist, dass Sie NULL enthalten. Wenn Sie mit dieser Funktion noch nicht vertraut sind, sollten Sie sich mit der Dokumentation vertraut machen, indem Sie die c#-Dokumentation lesen.

Auf dieser Seite wird EF Core Unterstützung für Verweis Typen eingeführt, die NULL-Werte zulassen, und es werden bewährte Methoden zum Arbeiten mit diesen beschrieben.

## <a name="required-and-optional-properties"></a>Erforderliche und optionale Eigenschaften

Die Haupt Dokumentation zu den erforderlichen und optionalen Eigenschaften sowie deren Interaktion mit Verweis Typen, die NULL-Werte zulassen, ist die [erforderliche und optionale Eigenschaften](xref:core/modeling/entity-properties#required-and-optional-properties) Seite. Es wird empfohlen, dass Sie zunächst die Seite lesen.

> [!NOTE]
> Vorsicht beim Aktivieren von Verweis Typen, die NULL-Werte zulassen, für ein vorhandenes Projekt: Verweistyp Eigenschaften, die zuvor als optional konfiguriert wurden, werden nun als erforderlich konfiguriert, es sei denn, Sie sind explizit mit Anmerkungen versehen, die NULL-Werte zulassen. Wenn Sie ein relationales Datenbankschema verwalten, kann dies dazu führen, dass Migrationen generiert werden, die die NULL-Zulässigkeit der Daten Bank Spalte ändern.

## <a name="non-nullable-properties-and-initialization"></a>Eigenschaften und Initialisierung, die keine NULL-Werte zulassen

Wenn Verweis Typen, die NULL-Werte zulassen, aktiviert sind, gibt der c#-Compiler Warnungen für jede nicht initialisierte Eigenschaft aus, die keine NULL-Werte zulässt, da diese NULL enthalten würden. Folglich kann die folgende, gängige Methode zum Schreiben von Entitäts Typen nicht verwendet werden:

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/CustomerWithWarning.cs?name=CustomerWithWarning&highlight=4-5)]

Die [konstruktorbindung](xref:core/modeling/constructors) ist eine nützliche Methode, um sicherzustellen, dass die Eigenschaften, die keine NULL-Werte zulassen, initialisiert werden:

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/CustomerWithConstructorBinding.cs?name=CustomerWithConstructorBinding&highlight=6-9)]

Leider ist die konstruktorbindung in einigen Szenarios keine Option. Navigations Eigenschaften können z. b. nicht auf diese Weise initialisiert werden.

Erforderliche Navigations Eigenschaften stellen eine zusätzliche Schwierigkeit dar: Obwohl eine abhängige für einen bestimmten Prinzipal immer vorhanden ist, kann Sie von einer bestimmten Abfrage geladen werden, abhängig von den Anforderungen zu diesem Zeitpunkt im Programm (Weitere Informationen[finden Sie unter unterschiedliche Muster zum Laden von Daten](xref:core/querying/related-data)). Gleichzeitig ist es nicht wünschenswert, dass diese Eigenschaften auf NULL festgelegt werden, da dadurch der gesamte Zugriff auf NULL erzwungen werden würde, auch wenn Sie erforderlich sind.

Eine Möglichkeit zum Umgang mit diesen Szenarien besteht darin, eine Eigenschaft, die keine NULL-Werte zulässt, mit einem dahinter liegenden [Feld](xref:core/modeling/backing-field)zu haben, das NULL-Werte zulässt

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Order.cs?range=10-17)]

Da die Navigations Eigenschaft keine NULL-Werte zulässt, wird eine erforderliche Navigation konfiguriert. solange die Navigation ordnungsgemäß geladen ist, kann auf den abhängigen über die-Eigenschaft zugegriffen werden. Wenn jedoch auf die Eigenschaft zugegriffen wird, ohne dass die zugehörige Entität zuvor ordnungsgemäß geladen wurde, wird eine InvalidOperationException ausgelöst, da der API-Vertrag falsch verwendet wurde. Beachten Sie, dass EF so konfiguriert werden muss, dass immer auf das dahinter liegende Feld und nicht auf die-Eigenschaft zugegriffen wird, da es darauf basiert, dass der Wert auch dann gelesen werden kann, wenn er nicht Lesen Sie die Dokumentation zu den entsprechenden [Feldern](xref:core/modeling/backing-field) , und `PropertyAccessMode.Field` Geben Sie ggf. an, um sicherzustellen, dass die Konfiguration korrekt ist.

Als terser Alternative ist es möglich, die-Eigenschaft mit der Hilfe des NULL-Operator (!) einfach mit NULL zu initialisieren:

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Order.cs?range=19)]

Ein tatsächlicher Null-Wert wird nie beobachtet, außer aufgrund eines Programmierfehlers, z. b. durch den Zugriff auf die Navigations Eigenschaft, ohne dass die zugehörige Entität vorab ordnungsgemäß geladen wird.

> [!NOTE]
> Sammlungs Navigation, die Verweise auf mehrere verknüpfte Entitäten enthalten, sollte immer keine NULL-Werte zulassen. Eine leere Auflistung bedeutet, dass keine verknüpften Entitäten vorhanden sind, aber die Liste selbst darf nie NULL sein.

## <a name="dbcontext-and-dbset"></a>Dbcontext und dbset

Die übliche Vorgehensweise, dass nicht initialisierte dbset-Eigenschaften für Kontext Typen verwendet werden, ist ebenfalls problematisch, da der Compiler jetzt Warnungen für Sie ausgibt. Dies kann wie folgt korrigiert werden:

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/NullableReferenceTypesContext.cs?name=Context&highlight=3-4)]

Eine andere Strategie besteht darin, nicht auf NULL festleg Bare Auto-Eigenschaften zu verwenden, diese jedoch mit NULL zu initialisieren, indem der NULL-verzweigende Operator (!) verwendet wird, um die Compilerwarnung zu stillen. Der dbcontext-Basiskonstruktor stellt sicher, dass alle dbset-Eigenschaften initialisiert werden und NULL niemals für Sie beachtet wird.

## <a name="navigating-and-including-nullable-relationships"></a>Navigieren in und einschließen von null-fähigen Beziehungen

Beim Umgang mit optionalen Beziehungen können Compilerwarnungen auftreten, bei denen eine tatsächliche NULL-Verweis Ausnahme nicht möglich wäre. Wenn Sie Ihre LINQ-Abfragen übersetzen und ausführen, stellt EF Core sicher, dass die Navigation zu dieser nicht ausgelöst wird, wenn eine optionale verknüpfte Entität nicht vorhanden ist. Der Compiler kennt diese EF Core Garantie jedoch nicht und führt Warnungen aus, als ob die LINQ-Abfrage im Arbeitsspeicher ausgeführt wurde, mit LINQ to Objects. Daher ist es erforderlich, dass der Compiler den NULL-Operator (!) verwendet, um den Compiler darüber zu informieren, dass ein tatsächlicher Null-Wert nicht möglich ist:

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Program.cs?range=46)]

Ein ähnliches Problem tritt auf, wenn mehrere Beziehungs Ebenen über optionale Navigationen eingeschlossen werden:

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Program.cs?range=36-39&highlight=2)]

Wenn Sie dies sehr viel tun und die fraglichen Entitäts Typen vorwiegend (oder exklusiv) in EF Core Abfragen verwendet werden, sollten Sie die Navigations Eigenschaften in Erwägung ziehen, die nicht auf NULL festgelegt werden können, und Sie über die fließende API oder Daten Anmerkungen als optional konfigurieren. Dadurch werden alle Compilerwarnungen entfernt, während die Beziehung optional bleibt. Wenn die Entitäten jedoch außerhalb EF Core durchlaufen werden, werden möglicherweise NULL-Werte beachtet, obwohl die Eigenschaften als nicht auf NULL festleg Bare Werte kommentiert werden.

## <a name="limitations"></a>Einschränkungen

* Reverse Engineering unterstützt zurzeit keine [c# 8-Verweis Typen (NULL-Werte zulassen)](/dotnet/csharp/tutorials/nullable-reference-types): EF Core generiert immer c#-Code, der voraussetzt, dass das Feature deaktiviert ist. Beispielsweise werden Textspalten, die NULL-Werte zulassen, als Eigenschaft mit dem Typ `string` erstellt, nicht `string?` mit der fließenden API oder den Daten Anmerkungen, mit denen konfiguriert wird, ob eine Eigenschaft erforderlich ist. Sie können den Gerüst Code bearbeiten und durch c#-Anmerkungen zur NULL-Zulässigkeit ersetzen. Die Gerüstbau Unterstützung für Verweis Typen, die NULL-Werte zulassen, wird von Issue [#15520](https://github.com/dotnet/efcore/issues/15520)
* Die öffentliche API-Oberfläche von EF Core wurde noch nicht für NULL-Zulässigkeit mit Anmerkungen versehen (die öffentliche API ist "Null-schräg"), was manchmal umständlich ist, wenn die NRT-Funktion aktiviert ist. Dies umfasst insbesondere die asynchronen LINQ-Operatoren, die von EF Core verfügbar gemacht werden, z. b. [firstordefaultasync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_). Wir planen, dies für die Version 5,0 zu beheben.
