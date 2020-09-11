---
title: Portieren von EF6 nach EF Core
description: Allgemeine Informationen zum Portieren einer Anwendung von Entity Framework 6 zu Entity Framework Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 826b58bd-77b0-4bbc-bfcd-24d1ed3a8f38
uid: efcore-and-ef6/porting/index
ms.openlocfilehash: 132934df2ef7929372c4a092635c5c97227983f9
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619685"
---
# <a name="porting-from-ef6-to-ef-core"></a>Portieren von EF 6 nach EF Core

Aufgrund der wesentlichen Änderungen in EF Core wird nicht empfohlen, eine EF 6-Anwendung auf EF Core umzustellen – es sei denn, es gibt einen zwingenden Grund für eine solche Änderung.
Sie sollten den Wechsel von EF 6 nach EF Core weniger als Upgrade, sondern als eine Portierung betrachten.

> [!IMPORTANT]
> Bevor Sie die Portierung starten, müssen Sie überprüfen, ob EF Core den Datenzugriffsanforderungen für Ihre Anwendung entspricht.

## <a name="missing-features"></a>Fehlende Features

Stellen Sie sicher, dass EF Core über alle Funktionen verfügt, die Sie in Ihrer Anwendung benötigen. Im [Funktionsvergleich](xref:efcore-and-ef6/index) finden Sie einen ausführlichen Vergleich der Funktionen in EF Core mit EF6. Wenn erforderliche Features fehlen, stellen Sie vor dem Portieren zu EF Core sicher, dass dies kompensiert werden kann.

## <a name="behavior-changes"></a>Verhaltensänderungen

Dies ist eine nicht vollständige Liste einiger Verhaltensänderungen zwischen EF6 und EF Core. Berücksichtigen Sie diese beim Portieren Ihrer Anwendung, da hierdurch das Verhalten der Anwendung geändert wird, dies aber nach der Umstellung auf EF Core nicht als Kompilierungsfehler angezeigt wird.

### <a name="dbsetaddattach-and-graph-behavior"></a>Dbset.Add/Attach und Graph-Verhalten

In EF6 führt das Aufrufen von `DbSet.Add()` für eine Entität zu einer rekursiven Suche nach allen Entitäten, auf die in den Navigationseigenschaften verwiesen wird. Alle Entitäten, die gefunden werden und nicht bereits vom Kontext nachverfolgt werden, werden ebenfalls als hinzugefügt gekennzeichnet. `DbSet.Attach()` verhält sich identisch, mit dem Unterschied, dass alle Entitäten als unverändert markiert werden.

**EF Core führt eine ähnliche rekursive Suche aus, allerdings mit etwas anderen Regeln.**

*  Die Stammentität befindet sich immer im angeforderten Zustand (hinzugefügt für `DbSet.Add` und unverändert für `DbSet.Attach`).

*  **Für Entitäten, die während der rekursiven Suche von Navigationseigenschaften gefunden werden:**

    *  **Wenn der Primärschlüssel der Entität im Speicher generiert wird**

        * Wenn der Primärschlüssel nicht auf einen Wert festgelegt ist, wird der Zustand auf hinzugefügt festgelegt. Der Primärschlüsselwert wird als nicht festgelegt betrachtet, wenn ihm der CLR-Standardwert für den Eigenschaftentyp zugewiesen ist (z. B. `0` für `int`, `null` für `string` usw.).

        * Wenn der Primärschlüssel auf einen Wert festgelegt ist, wird der Zustand auf unverändert festgelegt.

    *  Wenn der Primärschlüssel nicht von der Datenbank generiert wird, wird die Entität in denselben Zustand wie der Stamm versetzt.

### <a name="code-first-database-initialization"></a>Code First-Datenbankinitialisierung

**EF6 verfügt über eine Vielzahl von Kriterien zum Auswählen der Datenbankverbindung und Initialisieren der Datenbank. Zu diesen Regeln gehören:**

* Wenn keine Konfiguration durchgeführt wird, wählt EF6 eine Datenbank in SQL Express oder LocalDb aus.

* Wenn eine Verbindungszeichenfolge mit dem gleichen Namen wie der Kontext in der `App/Web.config`-Datei der Anwendung vorhanden ist, wird diese Verbindung verwendet.

* Wenn die Datenbank noch nicht vorhanden ist, wird sie erstellt.

* Wenn keine der Tabellen aus dem Modell in der Datenbank vorhanden ist, wird das Schema für das aktuelle Modell der Datenbank hinzugefügt. Wenn Migrationen aktiviert sind, werden sie verwendet, um die Datenbank zu erstellen.

* Wenn die Datenbank vorhanden ist und das Schema bereits von EF6 erstellt wurde, wird das Schema auf Kompatibilität mit dem aktuellen Modell geprüft. Wenn sich das Modell seit der Erstellung des Schemas geändert hat, wird eine Ausnahme ausgelöst.

**EF Core führt diese Aktionen nicht aus.**

* Die Datenbankverbindung muss explizit im Code konfiguriert werden.

* Es wird keine Initialisierung ausgeführt. Sie müssen `DbContext.Database.Migrate()` verwenden, um Migrationen anzuwenden (oder `DbContext.Database.EnsureCreated()` und `EnsureDeleted()`, um die Datenbank ohne Migrationen zu erstellen bzw. zu löschen).

### <a name="code-first-table-naming-convention"></a>Benennungskonvention für Code First-Tabelle

EF6 führt den Entitätsklassennamen über einen Pluralisierungsdienst aus, um den Standardtabellennamen zu berechnen, dem die Entität zugeordnet wird.

EF Core verwendet den Namen der `DbSet`-Eigenschaft, in der die Entität für den abgeleiteten Kontext verfügbar gemacht wird. Wenn die Entität nicht über eine `DbSet`-Eigenschaft verfügt, wird der Klassenname verwendet.
