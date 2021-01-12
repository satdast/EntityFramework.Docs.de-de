---
title: Indizes-EF Core
description: Konfigurieren von Indizes in einem Entity Framework Core Modell
author: roji
ms.date: 12/16/2019
uid: core/modeling/indexes
ms.openlocfilehash: ab81b108c4ff518cf98b7e835da3553c0c41efed
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128536"
---
# <a name="indexes"></a>Indizes

Indizes sind ein gängiges Konzept in vielen Daten speichern. Während Ihre Implementierung im Datenspeicher variieren kann, werden Sie verwendet, um Suchvorgänge auf Grundlage einer Spalte (oder einer Gruppe von Spalten) effizienter zu gestalten. Weitere Informationen zur guten Index Verwendung finden Sie im [Abschnitt "Indizes](xref:core/performance/efficient-querying#use-indexes-properly) " in der Leistungsdokumentation.

Sie können einen Index für eine Spalte wie folgt angeben:

## <a name="data-annotations"></a>[Daten Anmerkungen](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Index.cs?name=Index&highlight=1)]

> [!NOTE]
> Das Konfigurieren von Indizes über Daten Anmerkungen wurde in EF Core 5,0 eingeführt.

## <a name="fluent-api"></a>[Fluent-API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Index.cs?name=Index&highlight=4)]

***

> [!NOTE]
> Gemäß der Konvention wird ein Index in jeder Eigenschaft (oder einem Satz von Eigenschaften) erstellt, die als Fremdschlüssel verwendet wird.
>
> EF Core unterstützt nur einen Index pro eindeutigem Satz von Eigenschaften. Wenn Sie einen Index für eine Gruppe von Eigenschaften konfigurieren, für die bereits ein Index festgelegt wurde (entweder durch Konvention oder vorherige Konfiguration), ändern Sie die Definition des Indexes. Dies ist hilfreich, wenn Sie einen von der Konvention erstellten Index weiter konfigurieren möchten.

## <a name="composite-index"></a>Zusammengesetzter Index

Ein Index kann sich auch über mehrere Spalten erstrecken:

### <a name="data-annotations"></a>[Daten Anmerkungen](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IndexComposite.cs?name=Composite&highlight=1)]

### <a name="fluent-api"></a>[Fluent-API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexComposite.cs?name=Composite&highlight=4)]

**_

Indizes für mehrere Spalten, auch bekannt als _composite Indizes *, beschleunigen Abfragen, die nach Index Spalten filtern, aber auch Abfragen, die nur nach den *ersten* Spalten filtern, die vom Index abgedeckt werden. Weitere Informationen finden Sie in der Dokumentation zur [Leistung](xref:core/performance/efficient-querying#use-indexes-properly) .

## <a name="index-uniqueness"></a>Index Eindeutigkeit

Standardmäßig sind Indizes nicht eindeutig: mehrere Zeilen dürfen für den Spalten Satz des Indexes die gleichen Werte aufweisen. Sie können einen Index wie folgt eindeutig gestalten:

### <a name="data-annotations"></a>[Daten Anmerkungen](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IndexUnique.cs?name=IndexUnique&highlight=1)]

### <a name="fluent-api"></a>[Fluent-API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexUnique.cs?name=IndexUnique&highlight=5)]

***

Wenn Sie versuchen, mehr als eine Entität mit denselben Werten für den Spalten Satz des Indexes einzufügen, wird eine Ausnahme ausgelöst.

## <a name="index-name"></a>Indexname

Gemäß der Konvention werden Indizes, die in einer relationalen Datenbank erstellt werden, benannt `IX_<type name>_<property name>` . Wird bei zusammengesetzten Indizes `<property name>` zu einer durch Trennzeichen getrennten Liste von Eigenschaftsnamen.

Sie können den Namen des Indexes festlegen, der in der Datenbank erstellt wurde:

### <a name="data-annotations"></a>[Daten Anmerkungen](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IndexName.cs?name=IndexName&highlight=1)]

### <a name="fluent-api"></a>[Fluent-API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexName.cs?name=IndexName&highlight=5)]

***

## <a name="index-filter"></a>Index Filter

Einige relationale Datenbanken ermöglichen es Ihnen, einen gefilterten oder partiellen Index anzugeben. Auf diese Weise können Sie nur eine Teilmenge der Spaltenwerte indizieren, die Größe des Indexes verringern und sowohl die Leistung als auch die Speicherplatz Auslastung verbessern. Weitere Informationen zu SQL Server gefilterten Indizes finden Sie in [der Dokumentation](/sql/relational-databases/indexes/create-filtered-indexes)zu.

Sie können die fließende API verwenden, um einen Filter für einen Index anzugeben, der als SQL-Ausdruck bereitgestellt wird:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexFilter.cs?name=IndexFilter&highlight=5)]

Bei Verwendung des SQL Server Anbieters fügt EF einen `'IS NOT NULL'` Filter für alle Spalten hinzu, die NULL-Werte zulassen, die Teil eines eindeutigen Indexes sind. Um diese Konvention zu überschreiben, können Sie einen `null` Wert angeben.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexNoFilter.cs?name=IndexNoFilter&highlight=6)]

## <a name="included-columns"></a>Eingeschlossene Spalten

Mit einigen relationalen Datenbanken können Sie eine Gruppe von Spalten konfigurieren, die in den Index aufgenommen werden, aber nicht Teil Ihres "Schlüssels" sind. Dies kann die Abfrageleistung erheblich verbessern, wenn alle Spalten in der Abfrage in den Index als Schlüssel-oder nicht Schlüssel Spalten eingeschlossen werden, da auf die Tabelle selbst nicht zugegriffen werden muss. Weitere Informationen zu SQL Server enthaltenen Spalten finden Sie in [der Dokumentation](/sql/relational-databases/indexes/create-indexes-with-included-columns)zu.

Im folgenden Beispiel `Url` ist die Spalte Teil des Index Schlüssels, sodass jede Abfrage Filterung für diese Spalte den Index verwenden kann. Außerdem müssen Abfragen, die nur auf die `Title` Spalten und zugreifen, `PublishedOn` nicht auf die Tabelle zugreifen und effizienter ausgeführt werden:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexInclude.cs?name=IndexInclude&highlight=5-9)]
