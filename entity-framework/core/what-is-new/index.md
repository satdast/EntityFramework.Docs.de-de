---
title: EF Core-Releases und Planung
description: Aktuelle EF Core-Releases und Zeitplan/Planungsdetails für künftige Releases
author: ajcvickers
ms.date: 01/12/2021
uid: core/what-is-new/index
ms.openlocfilehash: 18d8055840b5a38dc62d20e7e18c440b7360c9e4
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128328"
---
# <a name="ef-core-releases-and-planning"></a>EF Core-Releases und Planung

## <a name="stable-releases"></a>Stabile Releases

| Freigabe | Zielframework | Support bis zum | Links
|:--------|------------------|-----------------|------
| [EF Core 5.0](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore) | .NET Standard 2.1 | Mitte Februar 2022 | [Ankündigung](https://devblogs.microsoft.com/dotnet/announcing-the-release-of-ef-core-5-0/) / [Wichtige Änderungen](xref:core/what-is-new/ef-core-5.0/breaking-changes)
| [EF Core 3.1](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/3.1.10) | .NET-Standard 2.0 | 3\. Dezember 2022 (LTS) | [Ankündigung](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-3-1-and-entity-framework-6-4/)
| ~~[EF Core 3.0](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/3.0.3)~~ | .NET Standard 2.1 | Abgelaufen: 3. März 2020 | [Ankündigung](https://devblogs.microsoft.com/dotnet/announcing-ef-core-3-0-and-ef-6-3-general-availability/) / [Wichtige Änderungen](xref:core/what-is-new/ef-core-3.x/breaking-changes)
| ~~[EF Core 2.2](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/2.2.6)~~ | .NET-Standard 2.0 | Am 23. Dezember 2019 abgelaufen | [Ankündigung](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-2-2/)
| [EF Core 2.1](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/2.1.14) | .NET-Standard 2.0 | 21. August 2021 (LTS) | [Ankündigung](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-2-1/)
| ~~[EF Core 2.0](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/2.0.3)~~ | .NET-Standard 2.0 | Am 1. Oktober 2018 abgelaufen | [Ankündigung](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-2-0/)
| ~~[EF Core 1.1](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/1.1.6)~~ | .NET Standard 1.3 | Am 27. Juni 2019 abgelaufen | [Ankündigung](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-1-1/)
| ~~[EF Core 1.0](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/1.0.6)~~ | .NET Standard 1.3 | Am 27. Juni 2019 abgelaufen | [Ankündigung](https://devblogs.microsoft.com/dotnet/entity-framework-core-1-0-0-available/)

Weitere Informationen über die speziellen Plattformen, die von den jeweiligen EF Core-Releases unterstützt werden, finden Sie unter [Unterstützte Plattformen](xref:core/miscellaneous/platforms).

Weitere Informationen zum Ablaufdatum des Supports und zu Releases mit langfristigem Support (long-term support, LTS) finden Sie unter [Supportrichtlinie für .NET](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

## <a name="guidance-on-updating-to-new-releases"></a>Anleitung zum Aktualisieren auf neue Releases

* Unterstützte Releases werden auf Sicherheits- und andere kritische Fehler gepatcht. Verwenden Sie immer den aktuellen Patch eines vorhandenen Release. Verwenden Sie z. B. 2.1.x für EF Core 2.1 für das höchste verfügbare „x“.
* Aktualisierungen von Hauptversionen (z. B. EF Core 2 auf EF Core 3) beinhalten häufig wichtige Änderungen. Bei der Aktualisierung von Hauptversionen werden gründliche Tests empfohlen. Anleitungen zum Umgang mit wichtigen Änderungen finden Sie unter den Links „Wichtige Änderungen“.
* Kleinere Versionsaktualisierungen enthalten in der Regel keine wichtigen Änderungen. Dennoch wird ein gründlicher Test empfohlen, da neue Funktionen zu Regressionen führen können.

## <a name="release-planning-and-schedules"></a>Releaseplanung und Zeitpläne

Die EF Core-Releases werden mit dem [Veröffentlichungsplan von .NET Core](https://github.com/dotnet/core/blob/master/roadmap.md) abgestimmt.

Patchreleases werden normalerweise monatlich ausgeliefert, haben jedoch eine lange Vorlaufzeit.
Wir arbeiten daran, dies zu verbessern.

Weitere Informationen zur Entscheidung, was in den einzelnen Releases ausgeliefert werden soll, finden Sie unter [Die Releaseplanung](xref:core/what-is-new/release-planning).
Unsere detaillierte Planung geht in der Regel nicht über die nächste Haupt- oder Nebenversion hinaus.

## <a name="ef-core-60"></a>EF Core 6.0

Der nächste stabile Release auf **EF Core 6.0** ist für **November 2021** geplant.

Mithilfe der dokumentierten [Releaseplanung](xref:core/what-is-new/release-planning) wurde ein [allgemeiner Plan für EF Core 6.0](xref:core/what-is-new/ef-core-6.0/plan) erstellt.

Ihr Feedback zur Planung ist wichtig.
Sie können für ein Problem auf GitHub abstimmen (Daumen hoch 👍) und so angeben, dass dieses Problem wichtig ist.
Diese Daten werden dann in den Planungsprozess für das nächste Release aufgenommen.

### <a name="get-it-now"></a>Jetzt anfordern

Die Pakete von EF Core 6.0 sind **ab sofort**

* als [tägliche Builds](https://github.com/dotnet/aspnetcore/blob/master/docs/DailyBuilds.md) verfügbar:
  * Hier sind alle aktuellen Features und Fehlerbehebungen enthalten. Die Pakete sind in der Regel sehr stabil. Für jede Buildversion werden mehr als 75.000 Testläufe durchgeführt.

Außerdem werden häufige Vorschauversionen im Verlauf an NuGet gepusht. Beachten Sie, dass diese Vorschauversionen zeitlich hinter den Builds zurückliegen. Es wird aber getestet, ob sie mit den entsprechenden Vorschauversionen von ASP.NET Core und .NET Core funktionieren.

Mithilfe der Vorschauversionen oder der täglichen Builds können Sie Probleme ausfindig machen und so früh wie möglich Feedback geben.
Je früher wir solches Feedback erhalten, desto wahrscheinlicher ist eine Umsetzung vor dem nächsten Release.
