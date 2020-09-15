---
title: Kaskadierendes Delete – EF Core
description: Konfigurieren von Löschverhalten für verknüpfte Entitäten bei Löschung einer Prinzipalentität
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: ee8e14ec-2158-4c9c-96b5-118715e2ed9e
uid: core/saving/cascade-delete
ms.openlocfilehash: cf17e6586b89376b2d7fcc0f9cbfc8e1c4f6ba58
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617402"
---
# <a name="cascade-delete"></a>Kaskadierendes Delete

Kaskadierendes Delete wird in der Datenbankterminologie häufig für die Beschreibung eines Merkmals verwendet, durch das beim Löschen einer Zeile automatisch das Löschen verknüpfter Zeilen ausgelöst wird. Ein eng damit verbundenes Löschverhalten, das ebenfalls von EF Core abgedeckt wird, besteht im automatischen Löschen einer untergeordneten Entität, wenn ihre Beziehung zu einer übergeordneten Entität getrennt wurde. Dies ist allgemein bekannt unter „Löschen verwaister Entitäten“.

EF Core implementiert mehrere unterschiedliche Verhaltensweisen zum Löschen und ermöglicht die Konfiguration dieser Verhaltensweisen für einzelne Beziehungen. Darüber hinaus implementiert EF Core Konventionen, mit denen basierend auf der [Erforderlichkeit der Beziehung](xref:core/modeling/relationships#required-and-optional-relationships) automatisch hilfreiches Standardlöschverhalten für die einzelnen Beziehungen konfiguriert wird.

## <a name="delete-behaviors"></a>Löschverhalten

Das Löschverhalten wird im Enumeratortyp *DeleteBehavior* definiert und kann an die Fluent-API *OnDelete* übergeben werden, um zu steuern, ob das Löschen einer Prinzipalentität/übergeordneten Entität oder die Trennung der Beziehung zu abhängigen/untergeordneten Entitäten eine Nebenwirkung auf die abhängigen/untergeordneten Entitäten haben soll.

EF kann drei Aktionen ausführen, wenn eine Prinzipalentität/übergeordnete Entität gelöscht oder die Beziehung zur untergeordneten Entität gelöscht wird:

* Die untergeordnete/abhängige Entität kann gelöscht werden
* Die Fremdschlüsselwerte der untergeordneten Entität können auf NULL festgelegt werden
* Die untergeordnete Entität bleibt unverändert

> [!NOTE]  
> Das im EF Core-Modell konfigurierte Löschverhalten wird nur angewendet, wenn die Prinzipalentität mit EF Core gelöscht wird und die abhängigen Entitäten in den Speicher geladen werden (d.h. für nachverfolgte abhängige Entitäten). In der Datenbank muss ein entsprechendes kaskadierendes Verhalten eingerichtet werden, um sicherzustellen, dass auf Daten, die nicht vom Kontext nachverfolgt werden, die erforderliche Aktion angewendet wird. Wenn Sie die Datenbank mit EF Core erstellen, wird dieses kaskadierende Verhalten für Sie eingerichtet.

Bei der zweiten, oben aufgeführten Aktion ist das Festlegen eines Fremdschlüsselwerts nicht gültig, wenn der Fremdschlüssel keine NULL-Werte zulässt. (Ein Fremdschlüssel, der keine NULL-Werte zulässt, entspricht einer erforderlichen Beziehung.) In diesen Fällen verfolgt EF Core nach, ob die Fremdschlüsseleigenschaft bis zum Aufrufen von SaveChanges mit NULL markiert wurde. Zu diesem Zeitpunkt wird eine Ausnahme ausgelöst, da die Änderung in der Datenbank nicht beibehalten werden kann. Dies ist vergleichbar mit dem Abrufen einer Einschränkungsverletzung aus der Datenbank.

Es gibt vier Verhaltensweisen zum Löschen, die in der nachfolgenden Tabelle aufgeführt werden.

### <a name="optional-relationships"></a>Optionale Beziehungen

Bei optionalen Beziehungen (NULL-Werte zulassender Fremdschlüssel) _kann_ ein NULL-Fremdschlüsselwert gespeichert werden. Dies hat folgende Auswirkungen:

| Name des Verhaltens               | Auswirkung auf abhängige/untergeordnete Entität im Speicher    | Auswirkung auf abhängige/untergeordnete Entität in der Datenbank  |
|:----------------------------|:---------------------------------------|:---------------------------------------|
| **Cascade**                 | Entitäten werden gelöscht                   | Entitäten werden gelöscht                   |
| **ClientSetNull** (Standard) | Fremdschlüsseleigenschaften werden auf NULL festgelegt | Keine                                   |
| **SetNull**                 | Fremdschlüsseleigenschaften werden auf NULL festgelegt | Fremdschlüsseleigenschaften werden auf NULL festgelegt |
| **Restrict**                | Keine                                   | Keine                                   |

### <a name="required-relationships"></a>Erforderliche Beziehungen

Bei erforderlichen Beziehungen (keine NULL-Werte zulassender Fremdschlüssel) kann _kein_ NULL-Fremdschlüsselwert gespeichert werden. Dies hat folgende Auswirkungen:

| Name des Verhaltens         | Auswirkung auf abhängige/untergeordnete Entität im Speicher | Auswirkung auf abhängige/untergeordnete Entität in der Datenbank |
|:----------------------|:------------------------------------|:--------------------------------------|
| **Cascade** (Standard) | Entitäten werden gelöscht                | Entitäten werden gelöscht                  |
| **ClientSetNull**     | Auslösung durch SaveChanges                  | Keine                                  |
| **SetNull**           | Auslösung durch SaveChanges                  | Auslösung durch SaveChanges                    |
| **Restrict**          | Keine                                | Keine                                  |

In den obigen Tabellen kann *Keiner* zu einer Einschränkungsverletzung führen. Wenn beispielsweise eine Prinzipalentität/untergeordnete Entität gelöscht wird, jedoch keine Maßnahmen zum Ändern des Fremdschlüssels einer abhängigen/untergeordneten Entität ergriffen werden, ist die Wahrscheinlichkeit groß, dass es in der Datenbank aufgrund einer Einschränkungsverletzung zu einer Auslösung durch SaveChanges kommt.

Allgemeines:

* Verwenden Sie *Cascade*, wenn es Entitäten gibt, die ohne übergeordnete Entität nicht vorhanden sein können und EF die untergeordnete Entität automatisch löschen soll.
  * Entitäten, die ohne übergeordnete Entität nicht vorhanden sein können, verwenden in der Regel erforderliche Beziehungen. Hierfür gilt *Cascade* als Standard.
* Verwenden Sie *ClientSetNull*, wenn es Entitäten gibt, die möglicherweise über eine übergeordnete Entität verfügen und EF den Fremdschlüssel für Sie auf NULL festlegen soll
  * Entitäten, die ohne übergeordnete Entität vorhanden sein können, verwenden in der Regel optionale Beziehungen. Hierfür gilt *ClientSetNull* als Standard.
  * Verwenden Sie *SetNull*, wenn in der Datenbank auch versucht werden soll, NULL-Werte an untergeordnete Fremdschlüssel zu verteilen, selbst wenn die untergeordnete Entität nicht geladen wurde. Beachten Sie jedoch, dass dieser Vorgang von der Datenbank unterstützt werden muss. Zudem kann eine derartige Konfiguration der Datenbank zu anderen Einschränkungen führen. Dadurch erweist sich diese Option in der Praxis häufig als unpraktisch. Aus diesem Grund ist *SetNull* nicht der Standard.
* Verwenden Sie *Restrict*, wenn EF Core eine Entität niemals automatisch löschen oder den Fremdschlüssel automatisch auf NULL festlegen soll. Beachten Sie, dass untergeordnete Entitäten und die zugehörigen Fremdschlüsselwerte hierfür in Ihrem Code manuell synchron gehalten werden müssen. Andernfalls werden Einschränkungsausnahmen ausgelöst.

> [!NOTE]
> Anders als bei EF 6 treten kaskadierende Effekte in EF Core nicht automatisch auf, sondern nur, wenn SaveChanges aufgerufen wird.

> [!NOTE]  
> **Änderungen in EF Core 2.0:** In vorherigen Releases hat *Restrict* dazu geführt, dass Eigenschaften optionaler Fremdschlüssel in nachverfolgten abhängigen Entitäten auf NULL festgelegt wurden. Dies war das Standardverhalten für optionale Beziehungen. In EF Core 2.0 wurde zur Darstellung dieses Verhaltens *ClientSetNull* eingeführt und als Standard für optionale Beziehungen festgelegt. Das Verhalten von *Restrict* wurde so angepasst, dass niemals Nebenwirkungen bei abhängigen Entitäten auftreten.

## <a name="entity-deletion-examples"></a>Beispiele für das Löschen von Entitäten

Der folgende Code ist Teil eines [Beispiels](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/CascadeDelete/), das heruntergeladen und ausgeführt werden kann. Das Beispiel zeigt, was bei jedem Löschverhalten bei optionalen und erforderlichen Beziehungen geschieht, wenn eine übergeordnete Entität gelöscht wird.

[!code-csharp[Main](../../../samples/core/Saving/CascadeDelete/Sample.cs#DeleteBehaviorVariations)]

Zum Verständnis werden die einzelnen Variationen im Folgenden ausführlich betrachtet.

### <a name="deletebehaviorcascade-with-required-or-optional-relationship"></a>DeleteBehavior.Cascade mit erforderlicher oder optionaler Beziehung

```console
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

  After deleting blog '1':
    Blog '1' is in state Deleted with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

  Saving changes:
    DELETE FROM [Posts] WHERE [PostId] = 1
    DELETE FROM [Posts] WHERE [PostId] = 2
    DELETE FROM [Blogs] WHERE [BlogId] = 1

  After SaveChanges:
    Blog '1' is in state Detached with 2 posts referenced.
      Post '1' is in state Detached with FK '1' and no reference to a blog.
      Post '2' is in state Detached with FK '1' and no reference to a blog.
```

* Blog ist als gelöscht markiert
* Beiträge bleiben anfangs unverändert, da es erst durch SaveChanges zu kaskadierendem Verhalten kommt
* SaveChanges sendet Löschvorgänge für abhängige/untergeordnete Entitäten (Beiträge) und anschließend für die Prinzipalentität/übergeordnete Entität (Blog)
* Nach dem Speichern werden alle Entitäten getrennt, da sie jetzt aus der Datenbank gelöscht wurden

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-required-relationship"></a>DeleteBehavior.ClientSetNull oder DeleteBehavior.SetNull mit erforderlicher Beziehung

``` output
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

  After deleting blog '1':
    Blog '1' is in state Deleted with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

  Saving changes:
    UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 1

  SaveChanges threw DbUpdateException: Cannot insert the value NULL into column 'BlogId', table 'EFSaving.CascadeDelete.dbo.Posts'; column does not allow nulls. UPDATE fails. The statement has been terminated.
```

* Blog ist als gelöscht markiert
* Beiträge bleiben anfangs unverändert, da es erst durch SaveChanges zu kaskadierendem Verhalten kommt
* SaveChanges versucht, den FS des Beitrags auf NULL festzulegen. Dieser Vorgang schlägt jedoch fehl, da der FS nicht auf NULL festgelegt werden kann

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-optional-relationship"></a>DeleteBehavior.ClientSetNull oder DeleteBehavior.SetNull mit optionaler Beziehung

``` output
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

  After deleting blog '1':
    Blog '1' is in state Deleted with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

  Saving changes:
    UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 1
    UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 2
    DELETE FROM [Blogs] WHERE [BlogId] = 1

  After SaveChanges:
    Blog '1' is in state Detached with 2 posts referenced.
      Post '1' is in state Unchanged with FK 'null' and no reference to a blog.
      Post '2' is in state Unchanged with FK 'null' and no reference to a blog.
```

* Blog ist als gelöscht markiert
* Beiträge bleiben anfangs unverändert, da es erst durch SaveChanges zu kaskadierendem Verhalten kommt
* SaveChanges versucht, den FS von abhängigen/untergeordneten Entitäten (Beiträgen) vor dem Löschen der Prinzipalentität/übergeordneten Entität (Blog) auf NULL festzulegen
* Nach dem Speichern wird die Prinzipalentität/übergeordnete Entität (Blog) gelöscht, die abhängigen/untergeordneten Entitäten (Beiträge) werden jedoch weiter nachverfolgt
* Die nachverfolgten abhängigen/untergeordneten Entitäten (Beiträge) verfügen jetzt über NULL-FS-Werte und ihr Verweis auf die gelöschte Prinzipalentität/übergeordnete Entität (Blog) wurde entfernt

### <a name="deletebehaviorrestrict-with-required-or-optional-relationship"></a>DeleteBehavior.Restrict mit erforderlicher oder optionaler Beziehung

``` output
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

  After deleting blog '1':
    Blog '1' is in state Deleted with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

  Saving changes:
  SaveChanges threw InvalidOperationException: The association between entity types 'Blog' and 'Post' has been severed but the foreign key for this relationship cannot be set to null. If the dependent entity should be deleted, then setup the relationship to use cascade deletes.
```

* Blog ist als gelöscht markiert
* Beiträge bleiben anfangs unverändert, da es erst durch SaveChanges zu kaskadierendem Verhalten kommt
* Da *Restrict* EF die Anweisung gibt, den FS nicht automatisch auf NULL festzulegen, bleibt der Wert ungleich NULL; es kommt zu einer Auslösung durch SaveChanges ohne Speichern

## <a name="delete-orphans-examples"></a>Beispiele für das Löschen verwaister Entitäten

Der folgende Code ist Teil eines [Beispiels](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/CascadeDelete/), das heruntergeladen und ausgeführt werden kann. Im Beispiel wird gezeigt, was bei jedem Löschverhalten bei optionalen und erforderlichen Beziehungen geschieht, wenn die Beziehung zwischen einer Prinzipalentität/übergeordneten Entität und den zugehörigen abhängigen/untergeordneten Entitäten getrennt wird. In diesem Beispiel wird die Beziehung durch Entfernen der abhängigen/untergeordneten Entitäten (Beiträge) aus der Navigationseigenschaft der Sammlung in der Prinzipalentität/übergeordneten Entität (Blog) getrennt. Das Verhalten ist jedoch identisch, wenn der Verweis von abhängigen/untergeordneten Entitäten auf die Prinzipalentität/übergeordnete Entität stattdessen auf NULL festgelegt wird.

[!code-csharp[Main](../../../samples/core/Saving/CascadeDelete/Sample.cs#DeleteOrphansVariations)]

Zum Verständnis werden die einzelnen Variationen im Folgenden ausführlich betrachtet.

### <a name="deletebehaviorcascade-with-required-or-optional-relationship"></a>DeleteBehavior.Cascade mit erforderlicher oder optionaler Beziehung

``` output
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

  After making posts orphans:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Modified with FK '1' and no reference to a blog.
      Post '2' is in state Modified with FK '1' and no reference to a blog.

  Saving changes:
    DELETE FROM [Posts] WHERE [PostId] = 1
    DELETE FROM [Posts] WHERE [PostId] = 2

  After SaveChanges:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Detached with FK '1' and no reference to a blog.
      Post '2' is in state Detached with FK '1' and no reference to a blog.
```

* Beiträge werden als „Geändert“ markiert, da der FS durch die Trennung der Beziehung als NULL markiert wurde
  * Wenn der FS nicht auf NULL festgelegt werden kann, wird der eigentliche Wert nicht geändert, auch wenn er als NULL markiert ist
* SaveChanges sendet Löschvorgänge für abhängige/untergeordnete Entitäten (Beiträge)
* Nach dem Speichern werden die abhängigen/untergeordneten Entitäten (Beiträge) getrennt, da sie jetzt aus der Datenbank gelöscht wurden

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-required-relationship"></a>DeleteBehavior.ClientSetNull oder DeleteBehavior.SetNull mit erforderlicher Beziehung

``` output
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

  After making posts orphans:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Modified with FK 'null' and no reference to a blog.
      Post '2' is in state Modified with FK 'null' and no reference to a blog.

  Saving changes:
    UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 1

  SaveChanges threw DbUpdateException: Cannot insert the value NULL into column 'BlogId', table 'EFSaving.CascadeDelete.dbo.Posts'; column does not allow nulls. UPDATE fails. The statement has been terminated.
```

* Beiträge werden als „Geändert“ markiert, da der FS durch die Trennung der Beziehung als NULL markiert wurde
  * Wenn der FS nicht auf NULL festgelegt werden kann, wird der eigentliche Wert nicht geändert, auch wenn er als NULL markiert ist
* SaveChanges versucht, den FS des Beitrags auf NULL festzulegen. Dieser Vorgang schlägt jedoch fehl, da der FS nicht auf NULL festgelegt werden kann

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-optional-relationship"></a>DeleteBehavior.ClientSetNull oder DeleteBehavior.SetNull mit optionaler Beziehung

``` output
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

  After making posts orphans:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Modified with FK 'null' and no reference to a blog.
      Post '2' is in state Modified with FK 'null' and no reference to a blog.

  Saving changes:
    UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 1
    UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 2

  After SaveChanges:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK 'null' and no reference to a blog.
      Post '2' is in state Unchanged with FK 'null' and no reference to a blog.
```

* Beiträge werden als „Geändert“ markiert, da der FS durch die Trennung der Beziehung als NULL markiert wurde
  * Wenn der FS nicht auf NULL festgelegt werden kann, wird der eigentliche Wert nicht geändert, auch wenn er als NULL markiert ist
* SaveChanges legt den FS von abhängigen/untergeordneten Entitäten auf NULL fest
* Nach dem Speichern verfügen die nachverfolgten abhängigen/untergeordneten Entitäten (Beiträge) jetzt über NULL-FS-Werte und ihr Verweis auf die gelöschte Prinzipalentität/übergeordnete Entität (Blog) wurde entfernt

### <a name="deletebehaviorrestrict-with-required-or-optional-relationship"></a>DeleteBehavior.Restrict mit erforderlicher oder optionaler Beziehung

``` output
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

  After making posts orphans:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Modified with FK '1' and no reference to a blog.
      Post '2' is in state Modified with FK '1' and no reference to a blog.

  Saving changes:
  SaveChanges threw InvalidOperationException: The association between entity types 'Blog' and 'Post' has been severed but the foreign key for this relationship cannot be set to null. If the dependent entity should be deleted, then setup the relationship to use cascade deletes.
```

* Beiträge werden als „Geändert“ markiert, da der FS durch die Trennung der Beziehung als NULL markiert wurde
  * Wenn der FS nicht auf NULL festgelegt werden kann, wird der eigentliche Wert nicht geändert, auch wenn er als NULL markiert ist
* Da *Restrict* EF die Anweisung gibt, den FS nicht automatisch auf NULL festzulegen, bleibt der Wert ungleich NULL; es kommt zu einer Auslösung durch SaveChanges ohne Speichern

## <a name="cascading-to-untracked-entities"></a>Weitergabe an nicht verfolgte Entitäten

Beim Aufruf von *SaveChanges* werden die Regeln des kaskadierenden Delete auf alle Entitäten angewendet, die vom Kontext nachverfolgt werden. Dies ist in allen oben aufgeführten Beispielen der Fall. Deshalb wurde SQL generiert, um die Prinzipalentität/übergeordnete Entität (Blog) und sämtliche abhängigen/untergeordneten Entitäten (Beiträge) zu löschen:

```sql
    DELETE FROM [Posts] WHERE [PostId] = 1
    DELETE FROM [Posts] WHERE [PostId] = 2
    DELETE FROM [Blogs] WHERE [BlogId] = 1
```

Wenn nur die Prinzipalentität geladen wird – beispielsweise beim Ausführen einer Abfrage für einen Blog ohne `Include(b => b.Posts)` zum Einbeziehen von Beiträgen – generiert SaveChanges SQL nur zum Löschen der Prinzipalentität/übergeordneten Entität:

```sql
    DELETE FROM [Blogs] WHERE [BlogId] = 1
```

Die abhängigen/untergeordneten Entitäten (Beiträge) werden nur gelöscht, wenn in der Datenbank ein entsprechendes kaskadierendes Verhalten konfiguriert wurde. Wenn Sie die Datenbank mit EF erstellen, wird dieses kaskadierende Verhalten für Sie eingerichtet.
