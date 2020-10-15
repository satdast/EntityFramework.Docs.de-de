---
title: 'Festlegen expliziter Werte für generierte Eigenschaften: EF Core'
description: Informationen zum Festlegen expliziter Werte für Eigenschaften, die als mit Entity Framework Core generiert konfiguriert wurden
author: ajcvickers
ms.date: 10/27/2016
uid: core/saving/explicit-values-generated-properties
ms.openlocfilehash: b3a31d8139b244bec72347cf20600b6c2b65c7d2
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062996"
---
# <a name="setting-explicit-values-for-generated-properties"></a>Festlegen expliziter Werte für generierte Eigenschaften

Eine generierte Eigenschaft ist eine Eigenschaft, deren Wert (über EF oder die Datenbank) generiert wird, wenn die Entität hinzugefügt oder aktualisiert wird. Weitere Informationen finden Sie unter [Generated Properties (Generierte Eigenschaften)](xref:core/modeling/generated-properties).

Es gibt möglicherweise Situationen, in denen Sie einen expliziten Wert für einen generierten Wert festlegen möchten, anstatt einen zu generieren.

> [!TIP]
> Das in diesem Artikel verwendete [Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/ExplicitValuesGenerateProperties/) finden Sie auf GitHub.

## <a name="the-model"></a>Die Model-Komponente

Das in diesem Artikel verwendete Modell enthält eine einzelne `Employee`-Entität.

[!code-csharp[Main](../../../samples/core/Saving/ExplicitValuesGenerateProperties/Employee.cs#Sample)]

## <a name="saving-an-explicit-value-during-add"></a>Speichern eines expliziten Werts während des Hinzufügens

Die Eigenschaft `Employee.EmploymentStarted` ist so konfiguriert, dass ihre Werte von der Datenbank für neue Entitäten generiert werden (mithilfe eines Standardwerts).

[!code-csharp[Main](../../../samples/core/Saving/ExplicitValuesGenerateProperties/EmployeeContext.cs#EmploymentStarted)]

Der folgende Code fügt zwei Mitarbeiter in die Datenbank ein.

* Für den ersten wird der Eigenschaft `Employee.EmploymentStarted` kein Wert zugewiesen, sodass sie weiterhin auf den CLR-Standardwert für `DateTime` festgelegt ist.
* Für den zweiten wird der explizite Wert `1-Jan-2000` festgelegt.

[!code-csharp[Main](../../../samples/core/Saving/ExplicitValuesGenerateProperties/Sample.cs#EmploymentStarted)]

Die Ausgabe zeigt, dass die Datenbank einen Wert für den ersten Mitarbeiter generiert hat und der explizite Wert für den zweiten Mitarbeiter verwendet wurde.

```output
1: John Doe, 1/26/2017 12:00:00 AM
2: Jane Doe, 1/1/2000 12:00:00 AM
```

### <a name="explicit-values-into-sql-server-identity-columns"></a>Einfügen von expliziten Werten in IDENTITY-Spalten von SQL Server

Gemäß der Konvention ist die Eigenschaft `Employee.EmployeeId` eine im Speicher generierte `IDENTITY`-Spalte.

In den meisten Fällen funktioniert der oben gezeigte Ansatz für Schlüsseleigenschaften. Zum Einfügen expliziter Werte in eine `IDENTITY`-Spalte von SQL Server müssen Sie jedoch `IDENTITY_INSERT` manuell aktivieren, bevor Sie `SaveChanges()` aufrufen.

> [!NOTE]
> Die Automatisierung dieses Vorgangs im SQL Server-Anbieter ist eine [Featureanforderung](https://github.com/aspnet/EntityFramework/issues/703), die sich im Backlog befindet.

[!code-csharp[Main](../../../samples/core/Saving/ExplicitValuesGenerateProperties/Sample.cs#EmployeeId)]

Die Ausgabe zeigt an, dass die angegebenen IDs in der Datenbank gespeichert wurden.

```output
100: John Doe
101: Jane Doe
```

## <a name="setting-an-explicit-value-during-update"></a>Festlegen eines expliziten Werts während eines Updates

Die Eigenschaft `Employee.LastPayRaise` ist so konfiguriert, dass ihre Werte während Updates von der Datenbank generiert werden.

[!code-csharp[Main](../../../samples/core/Saving/ExplicitValuesGenerateProperties/EmployeeContext.cs#LastPayRaise)]

> [!NOTE]
> EF Core löst standardmäßig eine Ausnahme aus, wenn Sie versuchen einen expliziten Wert für eine Eigenschaft zu speichern, die so konfiguriert ist, dass er während des Updates generiert wird. Sie können dies umgehen, indem Sie wie oben gezeigt `AfterSaveBehavior` in der Metadaten-API festlegen.

Außerdem gibt es in der Datenbank einen Trigger zum Generieren von Werten für die `LastPayRaise`-Spalte während `UPDATE`-Vorgängen.

[!code-sql[Main](../../../samples/core/Saving/ExplicitValuesGenerateProperties/employee_UPDATE.sql)]

Im folgenden Code wird das Gehalt von zwei Mitarbeitern in der Datenbank erhöht.

* Für den ersten wird der Eigenschaft `Employee.LastPayRaise` kein Wert zugewiesen, sodass sie weiterhin auf NULL festgelegt ist.
* Für den zweiten wird ein expliziter Wert von vor einer Woche festgelegt (Rückdatierung der Gehaltserhöhung).

[!code-csharp[Main](../../../samples/core/Saving/ExplicitValuesGenerateProperties/Sample.cs#LastPayRaise)]

Die Ausgabe zeigt, dass die Datenbank einen Wert für den ersten Mitarbeiter generiert hat und der explizite Wert für den zweiten Mitarbeiter verwendet wurde.

```output
1: John Doe, 1/26/2017 12:00:00 AM
2: Jane Doe, 1/19/2017 12:00:00 AM
```
