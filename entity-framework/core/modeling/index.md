---
title: Erstellen und Konfigurieren eines Modells – EF Core
description: Übersicht über das Erstellen und Konfigurieren eines Modells mit Entity Framework Core
author: AndriySvyryd
ms.date: 10/13/2020
uid: core/modeling/index
ms.openlocfilehash: 709e2bde60c8e2c31f0a39390624c5d31a9cfa08
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129199"
---
# <a name="creating-and-configuring-a-model"></a>Erstellen und Konfigurieren eines Modells

Entity Framework verwendet eine Reihe von Konventionen, um ein Modell basierend auf der Anordnung Ihrer Entitätsklassen zu erstellen. Sie können zusätzliche Konfigurationen angeben, um die gemäß den Konventionen gewonnenen Ergebnisse zu ergänzen und/oder zu überschreiben.

Dieser Artikel behandelt die Konfiguration, die auf ein Modell für einen beliebigen Datenspeicher und auf jede relationale Datenbank angewendet werden kann. Anbieter können auch eine Konfiguration aktivieren, die speziell für einen bestimmten Datenspeicher gilt. Dokumentation zu anbieterspezifischen Konfigurationen finden Sie im Abschnitt [Datenbankanbieter](xref:core/providers/index).

> [!TIP]
> Das in diesem Artikel verwendete [Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples) finden Sie auf GitHub.

## <a name="use-fluent-api-to-configure-a-model"></a>Verwenden der Fluent-API zum Konfigurieren eines Modells

Sie können die `OnModelCreating`-Methode in Ihrem abgeleiteten Kontext überschreiben und mit `ModelBuilder API` Ihr Modell konfigurieren. Dies ist die wirksamste Konfigurationsmethode und erlaubt die Angabe der Konfiguration, ohne die Entitätsklassen zu verändern. Die Fluent-API-Konfiguration hat die höchste Priorität und überschreibt Konventionen und Datenanmerkungen.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?highlight=12-14)]

### <a name="grouping-configuration"></a>Gruppieren der Konfiguration

Um die Größe der <xref:System.Data.Entity.DbContext.OnModelCreating%2A>-Methode zu reduzieren, kann die gesamte Konfiguration für einen Entitätstyp in eine separate Klasse extrahiert werden, indem <xref:Microsoft.EntityFrameworkCore.IEntityTypeConfiguration%601> implementiert wird.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/EntityTypeConfiguration.cs?Name=IEntityTypeConfiguration)]

Rufen Sie dann einfach die `Configure`-Methode aus `OnModelCreating` auf.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/EntityTypeConfiguration.cs?Name=ApplyIEntityTypeConfiguration)]

Es ist möglich, sämtliche Konfigurationen anzuwenden, die in Typen angegeben werden, indem `IEntityTypeConfiguration` in einer bestimmten Assembly implementiert wird.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/EntityTypeConfiguration.cs?Name=ApplyConfigurationsFromAssembly)]

> [!NOTE]
> Die Reihenfolge, in der die Konfigurationen angewendet werden, ist nicht definiert. Diese Methode sollte daher nur verwendet werden, wenn die Reihenfolge keine Rolle spielt.

## <a name="use-data-annotations-to-configure-a-model"></a>Verwenden von Datenanmerkungen zum Konfigurieren eines Modells

Sie können auch Attribute (sogenannte „Datenanmerkungen“) auf Ihre Klassen und Eigenschaften anwenden. Datenanmerkungen setzen Konventionen außer Kraft, werden aber ihrerseits von der Fluent-API-Konfiguration außer Kraft gesetzt.

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?highlight=15)]
