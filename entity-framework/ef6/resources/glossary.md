---
title: Entity Framework Glossar-EF6
description: Entity Framework 6-Glossar
author: divega
ms.date: 10/23/2016
uid: ef6/resources/glossary
ms.openlocfilehash: 819bdda133b427fa6f8fc1915cde07560e6ca568
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072888"
---
# <a name="entity-framework-glossary"></a>Entity Framework Glossar
## <a name="code-first"></a>Code First
Erstellen eines Entity Framework Modells mithilfe von Code. Das Modell kann eine vorhandene Datenbank oder eine neue Datenbank als Ziel haben.

## <a name="context"></a>Kontext
Eine-Klasse, die eine Sitzung mit der Datenbank darstellt, sodass Sie Daten Abfragen und speichern können. Ein Kontext wird von der dbcontext-Klasse oder der ObjectContext-Klasse abgeleitet.

## <a name="convention-code-first"></a>Konvention (Code First)
Eine Regel, die von Entity Framework verwendet wird, um die Form des Modells aus den Klassen abzuleiten.

## <a name="database-first"></a>Database First
Erstellen eines Entity Framework Modells mit dem EF-Designer, der auf eine vorhandene Datenbank abzielt.

## <a name="eager-loading"></a>Eager Loading
Ein Muster zum Laden verwandter Daten, bei denen eine Abfrage für einen Entitätstyp auch Verwandte Entitäten als Teil der Abfrage lädt.

## <a name="ef-designer"></a>EF-Designer
Ein visueller Designer in Visual Studio, der es Ihnen ermöglicht, mithilfe von Feldern und Linien ein Entity Framework Modell zu erstellen.

## <a name="entity"></a>Entität
Eine Klasse oder ein Objekt, die oder das Anwendungsdaten darstellt, z. B. Kunden, Bestellungen und Produkte.

## <a name="entity-data-model"></a>Entity Data Model
Ein Modell, das Entitäten und die Beziehungen zwischen Ihnen beschreibt. EF verwendet EDM, um das konzeptionelle Modell zu beschreiben, mit dem Entwickler programmiert werden. EDM basiert auf dem von Dr. Peter Chen eingeführten Entitäts Beziehungsmodell. Das EDM wurde ursprünglich mit dem Hauptziel entwickelt, das Common Data Model in einer Suite von Entwickler-und Server Technologien von Microsoft zu werden. EDM wird auch als Teil des odata-Protokolls verwendet.

## <a name="explicit-loading"></a>Explizites Laden
Ein Muster zum Laden von verknüpften Daten, bei denen verbundene Objekte durch Aufrufen einer API geladen werden.

## <a name="fluent-api"></a>Fluent-API
Eine API, die verwendet werden kann, um ein Code First Modell zu konfigurieren.

## <a name="foreign-key-association"></a>Fremdschlüssel Zuordnung
Eine Zuordnung zwischen Entitäten, bei der eine Eigenschaft, die den Fremdschlüssel darstellt, in der Klasse der abhängigen Entität enthalten ist. Das Produkt enthält z. b. eine CategoryID-Eigenschaft.

## <a name="identifying-relationship"></a>Identifikation der Beziehung
Eine Beziehung, in der der Primärschlüssel der Prinzipalentität Teil des Primärschlüssels der abhängigen Entität ist. Bei diesem Beziehungstyp kann die abhängige Entität nicht ohne die Prinzipalentität vorhanden sein.

## <a name="independent-association"></a>Unabhängige Zuordnung
Eine Zuordnung zwischen Entitäten, bei der keine Eigenschaft vorhanden ist, die den Fremdschlüssel in der Klasse der abhängigen Entität darstellt. Beispielsweise enthält eine Produktklasse eine Beziehung zur Kategorie, aber keine CategoryID-Eigenschaft. Entity Framework verfolgt den Zustand der Zuordnung unabhängig vom Zustand der Entitäten an den beiden Zuordnungs enden.

## <a name="lazy-loading"></a>Lazy Loading
Ein Muster zum Laden verwandter Daten, bei dem verknüpfte Objekte automatisch geladen werden, wenn auf eine Navigations Eigenschaft zugegriffen wird.

## <a name="model-first"></a>Model First
Erstellen eines Entity Framework Modells mit dem EF-Designer, das dann verwendet wird, um eine neue Datenbank zu erstellen.

## <a name="navigation-property"></a>Navigationseigenschaft
Eine Eigenschaft einer Entität, die auf eine andere Entität verweist. Das Produkt enthält z. b. eine Kategorie-Navigations Eigenschaft, und die Kategorie enthält eine Eigenschaft für die PRODUKTNAVIGATION.

## <a name="poco"></a>POCO
Das Akronym für das Plain Old CLR-Objekt. Eine einfache Benutzerklasse, die über keine Abhängigkeiten mit einem Framework verfügt. Im Kontext von EF ist eine Entitäts Klasse, die nicht von EntityObject abgeleitet wird, beliebige Schnittstellen implementiert oder alle in EF definierten Attribute enthält. Solche Entitäts Klassen, die vom Persistenzframework entkoppelt werden, werden auch als "Persistenz ignorierend" bezeichnet.  

## <a name="relationship-inverse"></a>Beziehung umgekehrt
Das gegenüberliegende Ende einer Beziehung, z. b. Product. Kategorie und Kategorie. Product.

## <a name="self-tracking-entity"></a>Entität mit selbst Nachverfolgung
Eine Entität, die aus einer Code Generierungs Vorlage erstellt wurde und bei der N-Tier-Entwicklung hilft.

## <a name="table-per-concrete-type-tpc"></a>Tabelle pro konkretem Typ (TPC)
Eine Methode zum Zuordnen der Vererbung, bei der jeder nicht abstrakte Typ in der Hierarchie einer separaten Tabelle in der Datenbank zugeordnet wird.

## <a name="table-per-hierarchy-tph"></a>Tabelle pro Hierarchie (TPH)
Eine Methode zum Zuordnen der Vererbung, in der alle Typen in der Hierarchie derselben Tabelle in der Datenbank zugeordnet werden. Eine diskriminatorspalte wird verwendet, um den Typ zu identifizieren, dem jede Zeile zugeordnet ist.

## <a name="table-per-type-tpt"></a>Tabelle pro Typ (TPT)
Eine Methode zum Zuordnen der Vererbung, bei der die allgemeinen Eigenschaften aller Typen in der Hierarchie derselben Tabelle in der Datenbank zugeordnet werden, aber Eigenschaften, die für jeden Typ eindeutig sind, werden einer separaten Tabelle zugeordnet.

## <a name="type-discovery"></a>Typermittlung
Der Prozess, bei dem die Typen identifiziert werden, die Teil eines Entity Framework Modells sein sollen.
