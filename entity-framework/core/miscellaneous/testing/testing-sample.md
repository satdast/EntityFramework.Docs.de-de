---
title: EF Core Testbeispiel-EF Core
description: Beispiel für das Testen von Anwendungen, die verwenden Entity Framework Core
author: ajcvickers
ms.date: 04/22/2020
uid: core/miscellaneous/testing/testing-sample
no-loc:
- Item
- Tag
- Items
- Tags
- items
- tags
ms.openlocfilehash: 07cde296f07a883ba6abf45f94a31e072fb6d6cb
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063048"
---
# <a name="ef-core-testing-sample"></a>EF Core Testbeispiel

> [!TIP]
> Den Code in diesem Dokument finden Sie auf GitHub als Ausführ [bares Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/).
> Beachten Sie, dass einige dieser Tests **erwartungsgemäß fehlschlagen**. Die Gründe hierfür werden im folgenden erläutert.

Dieses Dokument führt Sie durch ein Beispiel für das Testen von Code, der EF Core verwendet.

## <a name="the-application"></a>Anwendung

Das [Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/) enthält zwei Projekte:

- ItemsWebAPI: eine sehr einfache [Web-API, die durch ASP.net Core](/aspnet/core/tutorials/first-web-api) mit einem einzelnen Controller unterstützt wird.
- Tests: ein [xUnit](https://xunit.net/) -Testprojekt zum Testen des Controllers

### <a name="the-model-and-business-rules"></a>Die Modell-und Geschäftsregeln

Das Modell, das diese API unterstützt, verfügt über zwei Entitäts Typen: Items und Tags .

- Items Beachten Sie die Groß-/Kleinschreibung und eine Auflistung von Tags .
- Jede Tag verfügt über eine Bezeichnung und eine Anzahl, die angibt, wie oft Sie auf das angewendet wurde Item .
- Jeder Item darf nur einen Tag mit einer bestimmten Bezeichnung aufweisen.
  - Wenn ein Element mehrmals mit derselben Bezeichnung gekennzeichnet ist, wird die Anzahl der vorhandenen Tags mit dieser Bezeichnung inkrementiert, anstatt ein neues Tag zu erstellen.
- Beim Löschen eines Item sollten alle zugeordneten gelöscht werden Tags .

#### <a name="the-no-locitem-entity-type"></a>Der Item Entitätstyp

Der `Item` Entitätstyp:

[!code-csharp[ItemEntityType](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Item.cs?name=ItemEntityType)]

Und die Konfiguration in `DbContext.OnModelCreating` :

[!code-csharp[ConfigureItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/ItemsContext.cs?name=ConfigureItem)]

Beachten Sie, dass der Entitätstyp die Art und Weise einschränkt, wie er das Domänen Modell und die Geschäftsregeln widerspiegeln kann. Dies gilt insbesondere für:

- Der Primärschlüssel wird dem Feld direkt zugeordnet `_id` und nicht öffentlich verfügbar gemacht.
  - EF erkennt und verwendet den privaten Konstruktor, der den Wert und den Namen des Primärschlüssels akzeptiert.
- Die `Name` -Eigenschaft ist schreibgeschützt und wird nur im-Konstruktor festgelegt.
- Tags werden als bereitgestellt `IReadOnlyList<Tag>` , um beliebige Änderungen zu verhindern.
  - EF ordnet die- `Tags` Eigenschaft dem dahinter liegenden Feld zu, `_tags` indem die entsprechenden Namen abgeglichen werden.
  - Die `AddTag` -Methode nimmt eine tagbezeichnung an und implementiert die oben beschriebene Geschäftsregel.
    Das heißt, ein Tag wird nur für neue Bezeichnungen hinzugefügt.
    Andernfalls wird die Anzahl für eine vorhandene Bezeichnung inkrementiert.
- Die `Tags` Navigations Eigenschaft ist für eine n:1-Beziehung konfiguriert.
  - Es ist nicht erforderlich, dass eine Navigations Eigenschaft von Tag auf ist Item , sodass Sie nicht eingeschlossen wird.
  - Außerdem Tag definiert keine Fremdschlüssel Eigenschaft.
    Stattdessen erstellt und verwaltet EF eine Eigenschaft im Schatten Zustand.

#### <a name="the-no-loctag-entity-type"></a>Der Tag Entitätstyp

Der `Tag` Entitätstyp:

[!code-csharp[TagEntityType](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Tag.cs?name=TagEntityType)]

Und die Konfiguration in `DbContext.OnModelCreating` :

[!code-csharp[ConfigureTag](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/ItemsContext.cs?name=ConfigureTag)]

Ebenso wie Item wird Tag der Primärschlüssel ausgeblendet, und die Eigenschaft wird als schreibgeschützt definiert `Label` .

### <a name="the-no-locitemscontroller"></a>Der Items Controller

Der Web-API-Controller ist ziemlich einfach.
Er ruft eine `DbContext` aus dem Container für die Abhängigkeitsinjektion durch die Konstruktorinjektion ab:

[!code-csharp[Constructor](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=Constructor)]

Sie verfügt über Methoden, um alle Items oder eine Item mit einem bestimmten Namen zu erhalten:

[!code-csharp[Get](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=Get)]

Es verfügt über eine Methode zum Hinzufügen eines neuen Item :

[!code-csharp[PostItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=PostItem)]

Eine Methode zum Markieren eines Item mit einer Bezeichnung:

[!code-csharp[PostTag](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=PostTag)]

Und eine Methode zum Löschen eines Item und aller zugeordneten Tags :

[!code-csharp[DeleteItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=DeleteItem)]

Die meisten Validierungs-und Fehlerbehandlung wurde entfernt, um die Übersichtlichkeit zu verringern.

## <a name="the-tests"></a>Die Tests

Die Tests sind so organisiert, dass Sie mit mehreren Datenbankanbieter Konfigurationen ausgeführt werden:

- Der SQL Server-Anbieter, der der von der Anwendung verwendete Anbieter ist.
- Der SQLite-Anbieter
- Der SQLite-Anbieter mit in-Memory-SQLite-Datenbanken
- Der EF in-Memory Database-Anbieter

Dies wird erreicht, indem alle Tests in einer Basisklasse vorgenommen werden und dann von diesem geerbt wird, um Sie mit jedem Anbieter zu testen.

> [!TIP]
> Sie müssen die SQL Server Verbindungs Zeichenfolge ändern, wenn Sie nicht localdb verwenden.
> Unter [Testen mit SQLite](xref:core/miscellaneous/testing/sqlite) finden Sie Anleitungen zur Verwendung von SQLite für in-Memory-Tests.

Es wird erwartet, dass die folgenden beiden Tests fehlschlagen:

- `Can_remove_item_and_all_associated_tags` bei Ausführung mit dem EF in-Memory Database-Anbieter
- `Can_add_item_differing_only_by_case` bei Ausführung mit dem SQL Server-Anbieter

Dies wird im folgenden ausführlicher beschrieben.

### <a name="setting-up-and-seeding-the-database"></a>Einrichten und Seeding der Datenbank

XUnit erstellt, wie bei den meisten Test-Frameworks, eine neue Test Klasseninstanz für jeden Testlauf.
Außerdem führt xUnit keine Tests in einer bestimmten Testklasse parallel aus.
Dies bedeutet, dass wir die Datenbank im testkonstruktor einrichten und konfigurieren können. Sie werden für jeden Test in einem bekannten Zustand angezeigt.

> [!TIP]
> In diesem Beispiel wird die Datenbank für jeden Test neu erstellt.
> Dies funktioniert gut für SQLite-und EF-in-Memory Database Tests, kann jedoch mit anderen Datenbanksystemen, einschließlich SQL Server, einen erheblichen Aufwand verursachen.
> Die Vorgehensweisen zur Reduzierung dieses Aufwands werden bei der [gemeinsamen Nutzung von Datenbanken](xref:core/miscellaneous/testing/sharing-databases)behandelt.

Beim Ausführen der einzelnen Tests:

- Dbcontextoptions wird für den verwendeten Anbieter konfiguriert und an den Basisklassenkonstruktor übergeben.
  - Diese Optionen werden in einer Eigenschaft gespeichert und in den Tests zum Erstellen von dbcontext-Instanzen verwendet.
- Eine Seed-Methode wird aufgerufen, um die Datenbank zu erstellen und zu erstellen.
  - Mit der Seed-Methode wird sichergestellt, dass die Datenbank bereinigt wird, indem Sie gelöscht und anschließend neu erstellt wird.
  - Einige bekannte Test Entitäten werden erstellt und in der Datenbank gespeichert.

[!code-csharp[Seeding](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=Seeding)]

Jede konkrete Testklasse erbt dann von diesem.
Beispiel:

[!code-csharp[SqliteItemsControllerTest](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/SqliteItemsControllerTest.cs?name=SqliteItemsControllerTest)]

### <a name="test-structure"></a>Test Struktur

Obwohl die Anwendung eine Abhängigkeitsinjektion verwendet, ist dies bei den Tests nicht der Fall.
Hier wäre es in Ordnung, die Abhängigkeitsinjektion zu verwenden, aber der zusätzliche Code, der benötigt wird, hat nur wenig Wert.
Stattdessen wird ein dbcontext mithilfe von erstellt `new` und dann direkt als Abhängigkeit an den Controller weitergegeben.

Jeder Test führt dann die zu testende Methode auf dem Controller aus und bestätigt, dass die Ergebnisse erwartungsgemäß sind.
Beispiel:

[!code-csharp[CanGetItems](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanGetItems)]

Beachten Sie, dass unterschiedliche dbcontext-Instanzen verwendet werden, um die Datenbank zu starten und die Tests auszuführen.
Dadurch wird sichergestellt, dass der Test keine Entitäten verwendet, die vom Kontext nachverfolgt werden, wenn das Seeding durchgeführt wird.
Außerdem ist es besser, was in Web-Apps und-Diensten passiert.

Durch Tests, die die Datenbank mutieren, wird aus ähnlichen Gründen eine zweite dbcontext-Instanz im Test erstellt.
Das heißt, Sie erstellen einen neuen, sauberen Kontext und lesen ihn aus der Datenbank, um sicherzustellen, dass die Änderungen in der Datenbank gespeichert wurden.
Beispiel:

[!code-csharp[CanAddItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddItem)]

Zwei etwas mehr beteiligte Tests decken die Geschäftslogik um das Hinzufügen von ab tags .

[!code-csharp[CanAddTag](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddTag)]

[!code-csharp[CanUpTagCount](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanUpTagCount)]

## <a name="issues-using-different-database-providers"></a>Probleme bei der Verwendung verschiedener Datenbankanbieter

Das Testen mit einem anderen Datenbanksystem, das in der Produktionsanwendung verwendet wird, kann zu Problemen führen.
Diese werden auf der konzeptionellen Ebene im [Testcode behandelt, der EF Core verwendet](xref:core/miscellaneous/testing/index).
In den folgenden Abschnitten werden zwei Beispiele für solche Probleme behandelt, die von den Tests in diesem Beispiel demonstriert werden.

### <a name="test-passes-when-the-application-is-broken"></a>Test läuft, wenn die Anwendung beschädigt ist

Eine der Anforderungen für unsere Anwendung besteht darin, dass "die Items groß-und Kleinschreibung beachtet und eine Auflistung von" beachtet werden muss Tags .
Dies ist ziemlich einfach zu testen:

[!code-csharp[CanAddItemCaseInsensitive](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddItemCaseInsensitive)]

Das Ausführen dieses Tests für den EF-in-Memory Database gibt an, dass alles in Ordnung ist.
Bei der Verwendung von SQLite ist alles weiterhin einwandfrei.
Der Test schlägt jedoch fehl, wenn er auf SQL Server ausgeführt wird.

```output
System.InvalidOperationException : Sequence contains more than one element
   at System.Linq.ThrowHelper.ThrowMoreThanOneElementException()
   at System.Linq.Enumerable.Single[TSource](IEnumerable`1 source)
   at Microsoft.EntityFrameworkCore.Query.Internal.QueryCompiler.Execute[TResult](Expression query)
   at Microsoft.EntityFrameworkCore.Query.Internal.EntityQueryProvider.Execute[TResult](Expression expression)
   at System.Linq.Queryable.Single[TSource](IQueryable`1 source, Expression`1 predicate)
   at Tests.ItemsControllerTest.Can_add_item_differing_only_by_case()
```

Dies liegt daran, dass der EF-in-Memory Database und die SQLite-Datenbank standardmäßig die Groß-/Kleinschreibung beachten.
SQL Server hingegen wird die Groß-/Kleinschreibung nicht beachtet.

EF Core ändert diese Verhaltensweisen nicht, da das Erzwingen einer Änderung bei der Unterscheidung nach Groß-/Kleinschreibung eine erhebliche Auswirkung auf die Leistung haben kann.

Sobald wir wissen, dass es sich um ein Problem handelt, können wir die Anwendung beheben und Tests kompensieren.
Der Punkt hier ist jedoch, dass dieser Fehler übersehen werden kann, wenn nur der EF-in-Memory Database oder SQLite-Anbieter getestet wird.

### <a name="test-fails-when-the-application-is-correct"></a>Test schlägt fehl, wenn die Anwendung korrekt ist

Eine weitere Anforderung für die Anwendung besteht darin, dass das Löschen eines Item alle zugeordneten löschen soll Tags .
Auch hier leicht zu testen:

[!code-csharp[DeleteItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=DeleteItem)]

Dieser Test wird SQL Server und SQLite bestanden, schlägt jedoch mit dem EF-in-Memory Database fehl!

```output
Assert.False() Failure
Expected: False
Actual:   True
   at Tests.ItemsControllerTest.Can_remove_item_and_all_associated_tags()
```

In diesem Fall funktioniert die Anwendung ordnungsgemäß, da SQL Server [kaskadierte](xref:core/saving/cascade-delete)Löschvorgänge unterstützt.
SQLite unterstützt auch das Löschen von Lösch Vorgängen, wie bei den meisten relationalen Datenbanken, sodass das Testen auf SQLite funktioniert.
Auf der anderen Seite unterstützt das EF-in-Memory Database [keine kaskadierenden](https://github.com/dotnet/efcore/issues/3924)Löschvorgänge.
Dies bedeutet, dass dieser Teil der Anwendung nicht mit dem EF in-Memory Database-Anbieter getestet werden kann.
