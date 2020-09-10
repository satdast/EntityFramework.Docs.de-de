---
title: Sequenzen-EF Core
description: Konfigurieren von Sequenzen in einem Entity Framework Core Modell
author: roji
ms.date: 12/18/2019
ms.assetid: 94f81a92-3c72-4e14-912a-f99310374e42
uid: core/modeling/sequences
ms.openlocfilehash: 1a212f3944ca95ebc7675ff29b2687c82fdf7fc7
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619087"
---
# <a name="sequences"></a>Sequenzen

> [!NOTE]  
> Sequenzen werden in der Regel nur von relationalen Datenbanken unterstützt. Wenn Sie eine nicht relationale Datenbank (z. b. Cosmos) verwenden, überprüfen Sie die Datenbankdokumentation, um eindeutige Werte zu erstellen.

Eine Sequenz generiert eindeutige, sequenzielle numerische Werte in der Datenbank. Sequenzen sind keiner bestimmten Tabelle zugeordnet, und mehrere Tabellen können so eingerichtet werden, dass Sie Werte aus derselben Sequenz zeichnen.

## <a name="basic-usage"></a>Grundlegende Verwendung

Sie können eine Sequenz im Modell einrichten und Sie dann verwenden, um Werte für Eigenschaften zu generieren:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Sequence.cs?name=Sequence&highlight=3,7)]

Beachten Sie, dass das zum Generieren eines Werts aus einer Sequenz verwendete SQL-Datenbankspezifisch ist. Das obige Beispiel funktioniert auf SQL Server, schlägt jedoch für andere Datenbanken fehl. Weitere Informationen finden Sie in der Dokumentation der jeweiligen Datenbank.

## <a name="configuring-sequence-settings"></a>Konfigurieren von Sequenz Einstellungen

Sie können auch weitere Aspekte der Sequenz konfigurieren, z. b. das Schema, den Startwert, das Inkrement usw.:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SequenceConfiguration.cs?name=SequenceConfiguration&highlight=3-5)]
