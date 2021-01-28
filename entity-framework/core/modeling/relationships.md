---
title: Beziehungen-EF Core
description: Konfigurieren von Beziehungen zwischen Entitäts Typen bei Verwendung von Entity Framework Core
author: AndriySvyryd
ms.date: 10/01/2020
uid: core/modeling/relationships
ms.openlocfilehash: 93d129435a3583ac5f741cc27952fb702f415a01
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983468"
---
# <a name="relationships"></a>Beziehungen

Eine Beziehung definiert, wie zwei Entitäten miteinander in Beziehung stehen. In einer relationalen Datenbank wird dies durch eine FOREIGN KEY-Einschränkung repräsentiert.

> [!NOTE]
> Die meisten Beispiele in diesem Artikel verwenden eine 1: n-Beziehung, um die Konzepte zu veranschaulichen. Beispiele für 1:1-und m:n-Beziehungen finden Sie im Abschnitt [andere Beziehungsmuster](#other-relationship-patterns) am Ende des Artikels.

## <a name="definition-of-terms"></a>Definition von Begriffen

Es gibt eine Reihe von Begriffen, die zum Beschreiben von Beziehungen verwendet werden.

* **Abhängige Entität:** Dies ist die Entität, die die Fremdschlüssel Eigenschaften enthält. Wird manchmal auch als ' Child ' der Beziehung bezeichnet.

* **Prinzipal Entität:** Dies ist die Entität, die die Eigenschaften des primären/Alternativen Schlüssels enthält. Wird manchmal auch als "übergeordnetes Element" der Beziehung bezeichnet.

* **Prinzipal Schlüssel:** Die Eigenschaften, die die Prinzipal Entität eindeutig identifizieren. Hierbei kann es sich um den Primärschlüssel oder einen alternativen Schlüssel handeln.

* **Fremdschlüssel:** Die Eigenschaften in der abhängigen Entität, die zum Speichern der Prinzipal Schlüsselwerte für die verknüpfte Entität verwendet werden.

* **Navigations Eigenschaft:** Eine für die Prinzipal-und/oder abhängige Entität definierte Eigenschaft, die auf die verknüpfte Entität verweist.

  * Auflistungs **Navigations Eigenschaft:** Eine Navigations Eigenschaft, die Verweise auf viele Verwandte Entitäten enthält.

  * **Verweis Navigations Eigenschaft:** Eine Navigations Eigenschaft, die einen Verweis auf eine einzelne verknüpfte Entität enthält.

  * **Umgekehrte Navigations Eigenschaft:** Bei der Erörterung einer bestimmten Navigations Eigenschaft bezieht sich dieser Begriff auf die Navigations Eigenschaft am anderen Ende der Beziehung.

* **Selbst verweisende Beziehung:** Eine Beziehung, in der die abhängigen und die Prinzipal Entitäts Typen identisch sind.

Der folgende Code zeigt eine 1: n-Beziehung zwischen `Blog` und. `Post`

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/Full.cs#Full)]

* `Post` ist die abhängige Entität

* `Blog` ist die Prinzipal Entität

* `Blog.BlogId` ist der Prinzipal Schlüssel (in diesem Fall handelt es sich um einen Primärschlüssel anstelle eines alternativen Schlüssels)

* `Post.BlogId` ist der Fremdschlüssel

* `Post.Blog` ist eine Verweis Navigations Eigenschaft.

* `Blog.Posts` ist eine Auflistungs Navigations Eigenschaft.

* `Post.Blog` ist die umgekehrte Navigations Eigenschaft von `Blog.Posts` (und umgekehrt).

## <a name="conventions"></a>Konventionen

Standardmäßig wird eine Beziehung erstellt, wenn eine Navigations Eigenschaft für einen Typ erkannt wird. Eine Eigenschaft wird als Navigations Eigenschaft betrachtet, wenn der Typ, auf den Sie verweist, nicht vom aktuellen Datenbankanbieter als Skalartyp zugeordnet werden kann.

> [!NOTE]
> Die von der Konvention ermittelten Beziehungen richten sich immer nach dem Primärschlüssel der Prinzipal Entität. Um einen alternativen Schlüssel als Ziel zu verwenden, muss mithilfe der flüssigen API eine zusätzliche Konfiguration durchgeführt werden.

### <a name="fully-defined-relationships"></a>Vollständig definierte Beziehungen

Das gängigste Muster für Beziehungen besteht darin, dass für beide Enden der Beziehung Navigations Eigenschaften und eine Fremdschlüssel Eigenschaft definiert sind, die in der abhängigen Entitäts Klasse definiert ist.

* Wenn zwischen zwei Typen ein paar Navigations Eigenschaften gefunden wird, werden diese als umgekehrte Navigations Eigenschaften derselben Beziehung konfiguriert.

* Wenn die abhängige Entität eine Eigenschaft mit einem Namen enthält, der mit einem dieser Muster übereinstimmt, wird Sie als Fremdschlüssel konfiguriert:
  * `<navigation property name><principal key property name>`
  * `<navigation property name>Id`
  * `<principal entity name><principal key property name>`
  * `<principal entity name>Id`

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/Full.cs?name=Full&highlight=6,15-16)]

In diesem Beispiel werden die markierten Eigenschaften zum Konfigurieren der Beziehung verwendet.

> [!NOTE]
> Wenn die Eigenschaft der Primärschlüssel oder ein Typ ist, der nicht mit dem Prinzipal Schlüssel kompatibel ist, wird Sie nicht als Fremdschlüssel konfiguriert.

> [!NOTE]
> Vor EF Core 3,0 wurde die Eigenschaft mit dem gleichen Namen wie die Prinzipal Schlüsseleigenschaft [ebenfalls als Fremdschlüssel abgeglichen](https://github.com/dotnet/efcore/issues/13274) .

### <a name="no-foreign-key-property"></a>Keine Fremdschlüssel Eigenschaft

Es wird empfohlen, eine Fremdschlüssel Eigenschaft in der abhängigen Entitäts Klasse zu definieren, die jedoch nicht erforderlich ist. Wenn keine Fremdschlüssel Eigenschaft gefunden wird, wird eine [Schatten-Fremdschlüssel Eigenschaft](xref:core/modeling/shadow-properties) mit dem Namen `<navigation property name><principal key property name>` oder, `<principal entity name><principal key property name>` Wenn keine Navigation für den abhängigen Typ vorhanden ist, eingefügt.

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/NoForeignKey.cs?name=NoForeignKey&highlight=6,15)]

In diesem Beispiel ist der Schatten Fremdschlüssel, weil der vorangestellt wird, `BlogId` dass der Navigations Name redundant ist.

> [!NOTE]
> Wenn bereits eine Eigenschaft mit demselben Namen vorhanden ist, wird dem Namen der Schatten Eigenschaft eine Zahl angehängt.

### <a name="single-navigation-property"></a>Einzelne Navigations Eigenschaft

Das Einschließen von nur einer Navigations Eigenschaft (keine umgekehrte Navigation und keine Fremdschlüssel Eigenschaft) reicht aus, um eine Beziehung gemäß der Konvention zu definieren. Sie können auch eine einzelne Navigations Eigenschaft und eine Fremdschlüssel Eigenschaft haben.

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/OneNavigation.cs?name=OneNavigation&highlight=6)]

### <a name="limitations"></a>Einschränkungen

Wenn mehrere Navigations Eigenschaften zwischen zwei Typen definiert sind (d. h. mehr als nur ein Navigations Paar, das aufeinander zeigen), sind die Beziehungen, die von den Navigations Eigenschaften dargestellt werden, mehrdeutig. Sie müssen Sie manuell konfigurieren, um die Mehrdeutigkeit aufzulösen.

### <a name="cascade-delete"></a>Kaskadierendes Delete

Gemäß der Konvention wird CASCADE DELETE für erforderliche Beziehungen und *clientsetnull* für optionale Beziehungen auf *Cascade* festgelegt. *Cascade* bedeutet, dass abhängige Entitäten ebenfalls gelöscht werden. *Clientsetnull* bedeutet, dass abhängige Entitäten, die nicht in den Arbeitsspeicher geladen werden, unverändert bleiben und manuell gelöscht oder aktualisiert werden müssen, um auf eine gültige Prinzipal Entität zu verweisen. Bei Entitäten, die in den Arbeitsspeicher geladen werden, wird EF Core versucht, die Fremdschlüssel Eigenschaften auf NULL festzulegen.

Den Unterschied zwischen erforderlichen und optionalen Beziehungen finden Sie im Abschnitt [erforderliche und optionale Beziehungen](#required-and-optional-relationships) .

Weitere Informationen zu den verschiedenen Lösch Verhalten und den von der Konvention verwendeten Standardwerten finden Sie unter [Cascade Delete](xref:core/saving/cascade-delete) .

## <a name="manual-configuration"></a>Manuelle Konfiguration

### <a name="fluent-api"></a>[Fluent-API](#tab/fluent-api)

Um eine Beziehung in der fließenden API zu konfigurieren, müssen Sie zunächst die Navigations Eigenschaften ermitteln, die die Beziehung bilden. `HasOne` oder `HasMany` identifiziert die Navigations Eigenschaft für den Entitätstyp, für den Sie mit der Konfiguration beginnen. Anschließend verketten Sie einen Aufrufen von `WithOne` oder `WithMany` , um die umgekehrte Navigation zu ermitteln. `HasOne`/`WithOne`werden für Verweis Navigations Eigenschaften verwendet und für Auflistungs `HasMany` / `WithMany` Navigations Eigenschaften verwendet.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NoForeignKey.cs?name=NoForeignKey&highlight=8-10)]

### <a name="data-annotations"></a>[Datenanmerkungen](#tab/data-annotations)

Mit den Daten Anmerkungen können Sie konfigurieren, wie Navigations Eigenschaften für die abhängigen und Prinzipal Entitäten gekoppelt werden. Dies erfolgt in der Regel, wenn zwischen zwei Entitäts Typen mehr als ein paar Navigations Eigenschaften vorhanden ist.

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Relationships/InverseProperty.cs?name=InverseProperty&highlight=20,23)]

> [!NOTE]
> Sie können für die Eigenschaften der abhängigen Entität nur [Required] verwenden, um die Eignung der Beziehung zu beeinflussen. [Erforderlich] die Navigation von der Prinzipal Entität wird normalerweise ignoriert, kann jedoch dazu führen, dass die Entität zu einer abhängigen Entität wird.

> [!NOTE]
> Die Daten Anmerkungen `[ForeignKey]` und `[InverseProperty]` sind im- `System.ComponentModel.DataAnnotations.Schema` Namespace verfügbar. `[Required]` ist im- `System.ComponentModel.DataAnnotations` Namespace verfügbar.

---

### <a name="single-navigation-property"></a>Einzelne Navigations Eigenschaft

Wenn Sie nur über eine Navigations Eigenschaft verfügen, gibt es Parameter lose über Ladungen von `WithOne` und `WithMany` . Dies gibt an, dass ein Verweis oder eine Auflistung am anderen Ende der Beziehung konzeptionell ist, aber in der Entitäts Klasse ist keine Navigations Eigenschaft enthalten.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/OneNavigation.cs?name=OneNavigation&highlight=8-10)]

---

### <a name="configuring-navigation-properties"></a>Konfigurieren von Navigations Eigenschaften

> [!NOTE]
> Dieses Feature wurde in EF Core 5.0 eingeführt.

Nachdem die Navigations Eigenschaft erstellt wurde, müssen Sie Sie möglicherweise weiter konfigurieren.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NavigationConfiguration.cs?name=NavigationConfiguration&highlight=7-9)]

> [!NOTE]
> Dieser Befehl kann nicht zum Erstellen einer Navigations Eigenschaft verwendet werden. Sie wird nur verwendet, um eine Navigations Eigenschaft zu konfigurieren, die zuvor durch Definieren einer Beziehung oder einer Konvention erstellt wurde.

### <a name="foreign-key"></a>Fremdschlüssel

#### <a name="fluent-api-simple-key"></a>[Fließende API (einfacher Schlüssel)](#tab/fluent-api-simple-key)

Sie können die fließende API verwenden, um zu konfigurieren, welche Eigenschaft als Fremdschlüssel Eigenschaft für eine bestimmte Beziehung verwendet werden soll:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ForeignKey.cs?name=ForeignKey&highlight=11)]

#### <a name="fluent-api-composite-key"></a>[Fließende API (zusammengesetzter Schlüssel)](#tab/fluent-api-composite-key)

Mit der fließend-API können Sie konfigurieren, welche Eigenschaften als zusammengesetzte Fremdschlüssel Eigenschaften für eine bestimmte Beziehung verwendet werden sollen:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CompositeForeignKey.cs?name=CompositeForeignKey&highlight=13)]

#### <a name="data-annotations-simple-key"></a>[Daten Anmerkungen (einfacher Schlüssel)](#tab/data-annotations-simple-key)

Mit den Daten Anmerkungen können Sie konfigurieren, welche Eigenschaft als Fremdschlüssel Eigenschaft für eine bestimmte Beziehung verwendet werden soll. Dies erfolgt in der Regel, wenn die Fremdschlüssel Eigenschaft nicht gemäß der Konvention ermittelt wird:

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Relationships/ForeignKey.cs?name=ForeignKey&highlight=17)]

> [!TIP]
> Die-Anmerkung `[ForeignKey]` kann für jede Navigations Eigenschaft in der Beziehung platziert werden. Die Navigations Eigenschaft in der abhängigen Entitäts Klasse muss nicht verwendet werden.

> [!NOTE]
> Die mit `[ForeignKey]` einer Navigations Eigenschaft angegebene Eigenschaft muss nicht im abhängigen Typ vorhanden sein. In diesem Fall wird der angegebene Name verwendet, um einen Schatten Fremdschlüssel zu erstellen.

---

#### <a name="shadow-foreign-key"></a>Schatten-Fremdschlüssel

Sie können die Zeichen folgen Überladung von verwenden `HasForeignKey(...)` , um eine Schatten Eigenschaft als Fremdschlüssel zu konfigurieren (Weitere Informationen finden Sie unter [Schatten Eigenschaften](xref:core/modeling/shadow-properties) ). Es wird empfohlen, die Schatten Eigenschaft explizit dem Modell hinzuzufügen, bevor Sie als Fremdschlüssel verwendet wird (wie unten gezeigt).

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ShadowForeignKey.cs?name=ShadowForeignKey&highlight=10,16)]

#### <a name="foreign-key-constraint-name"></a>Name der FOREIGN KEY-Einschränkung

Gemäß der Konvention werden Foreign Key-Einschränkungen beim Ziel einer relationalen Datenbank als "FK" benannt \_ \<dependent type name> \_ \<principal type name> \_ \<foreign key property name> . Bei zusammengesetzten Fremdschlüsseln \<foreign key property name> wird zu einer durch Trennzeichen getrennten Liste von Fremdschlüssel Eigenschaftsnamen.

Sie können den Einschränkungs Namen auch wie folgt konfigurieren:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ConstraintName.cs?name=ConstraintName&highlight=6-7)]

### <a name="without-navigation-property"></a>Ohne Navigations Eigenschaft

Sie müssen nicht unbedingt eine Navigations Eigenschaft bereitstellen. Sie können auf einer Seite der Beziehung einfach einen Fremdschlüssel bereitstellen.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NoNavigation.cs?name=NoNavigation&highlight=8-11)]

### <a name="principal-key"></a>Prinzipal Schlüssel

Wenn Sie möchten, dass der Fremdschlüssel auf eine andere Eigenschaft als den Primärschlüssel verweist, können Sie mit der fließend-API die Prinzipal Schlüsseleigenschaft für die Beziehung konfigurieren. Die Eigenschaft, die Sie als Prinzipal Schlüssel konfigurieren, wird automatisch als [alternativer Schlüssel](xref:core/modeling/keys#alternate-keys)eingerichtet.

#### <a name="simple-key"></a>[Einfache Taste](#tab/simple-key)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/PrincipalKey.cs?name=PrincipalKey&highlight=11)]

#### <a name="composite-key"></a>[Zusammengesetzte Schlüssel](#tab/composite-key)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CompositePrincipalKey.cs?name=CompositePrincipalKey&highlight=11)]

> [!WARNING]
> Die Reihenfolge, in der Sie die Prinzipal Schlüsseleigenschaften angeben, muss mit der Reihenfolge identisch sein, in der Sie für den Fremdschlüssel angegeben werden.

---

### <a name="required-and-optional-relationships"></a>Erforderliche und optionale Beziehungen

Mit der fließend-API können Sie konfigurieren, ob die Beziehung erforderlich oder optional ist. Letztendlich steuert dies, ob die Fremdschlüssel Eigenschaft erforderlich oder optional ist. Dies ist besonders nützlich, wenn Sie einen Schatten Zustands-Fremdschlüssel verwenden. Wenn Sie über eine Fremdschlüssel Eigenschaft in der Entitäts Klasse verfügen, wird die Eignung der Beziehung abhängig davon bestimmt, ob die Fremdschlüssel Eigenschaft erforderlich oder optional ist (Weitere Informationen finden Sie unter [erforderliche und optionale Eigenschaften](xref:core/modeling/entity-properties#required-and-optional-properties) ).

Die Fremdschlüssel Eigenschaften befinden sich auf dem abhängigen Entitätstyp. Wenn Sie daher als erforderlich konfiguriert werden, bedeutet dies, dass für jede abhängige Entität eine entsprechende Prinzipal Entität erforderlich ist.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/Required.cs?name=Required&highlight=6)]

> [!NOTE]
> Durch `IsRequired(false)` das Aufrufen von wird auch die Fremdschlüssel Eigenschaft optional, sofern Sie nicht anderweitig konfiguriert ist.

### <a name="cascade-delete"></a>Kaskadierendes Delete

Sie können die fließende API verwenden, um das kaskadierte Lösch Verhalten für eine bestimmte Beziehung explizit zu konfigurieren.

Eine ausführliche Erläuterung der einzelnen Optionen finden Sie unter [Cascade Delete](xref:core/saving/cascade-delete) .

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CascadeDelete.cs?name=CascadeDelete&highlight=6)]

## <a name="other-relationship-patterns"></a>Andere Beziehungsmuster

### <a name="one-to-one"></a>1:1

Eine zu 1 Beziehung hat eine Verweis Navigations Eigenschaft auf beiden Seiten. Sie folgen denselben Konventionen wie 1: n-Beziehungen, aber es wird ein eindeutiger Index für die Fremdschlüssel Eigenschaft eingeführt, um sicherzustellen, dass nur ein abhängiger mit den einzelnen Prinzipalen verknüpft ist.

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/OneToOne.cs?name=OneToOne&highlight=6,15-16)]

> [!NOTE]
> EF wählt eine der Entitäten aus, die abhängig von der Fähigkeit, eine Fremdschlüssel Eigenschaft zu erkennen, abhängig ist. Wenn die falsche Entität als abhängig ausgewählt wird, können Sie diese mithilfe der flüssigen API korrigieren.

Beim Konfigurieren der Beziehung mit der flüssigen API verwenden Sie die `HasOne` -Methode und die- `WithOne` Methode.

Wenn Sie den Fremdschlüssel konfigurieren, müssen Sie den abhängigen Entitätstyp angeben. Beachten Sie den generischen Parameter, der `HasForeignKey` in der nachfolgenden Auflistung bereitgestellt wird. In einer 1: n-Beziehung ist klar, dass die Entität mit der Verweis Navigation die abhängige ist und die mit der Auflistung der Prinzipal ist. Dies ist jedoch in einer eins-zu-eins-Beziehung nicht der Fall, daher muss Sie explizit definiert werden.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/OneToOne.cs?name=OneToOne&highlight=11)]

Die abhängige Seite wird standardmäßig als optional betrachtet, kann jedoch als erforderlich konfiguriert werden. EF überprüft jedoch nicht, ob eine abhängige Entität bereitgestellt wurde, sodass diese Konfiguration nur einen Unterschied macht, wenn die Daten Bank Zuordnung die Durchsetzung zulässt. Ein häufiges Szenario hierfür sind Referenztypen, die standardmäßig Tabellen Aufteilung verwenden.

[!code-csharp[Main](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=Required&highlight=11-12)]

Mit dieser Konfiguration werden die Spalten, die entsprechen, `ShippingAddress` in der Datenbank als nicht auf NULL festlegbar gekennzeichnet.

> [!NOTE]
> Wenn Sie Verweis Typen verwenden, die keine [NULL-Werte](/dotnet/csharp/nullable-references) zulassen, ist das Aufrufen von `IsRequired` nicht erforderlich.

> [!NOTE]
> Die Möglichkeit, zu konfigurieren, ob die abhängige erforderlich ist, wurde in EF Core 5,0 eingeführt.

### <a name="many-to-many"></a>M:n

Für m:n-Beziehungen ist auf beiden Seiten eine Auflistungs Navigations Eigenschaft erforderlich. Sie werden gemäß der Konvention wie andere Beziehungstypen erkannt.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToManyShared.cs?name=ManyToManyShared)]

Die Art und Weise, in der diese Beziehung in der Datenbank implementiert wird, ist eine jointabelle, die Fremdschlüssel für `Post` und enthält `Tag` . Dies ist beispielsweise der Fall, den EF in einer relationalen Datenbank für das obige Modell erstellt.

```sql
CREATE TABLE [Posts] (
    [PostId] int NOT NULL IDENTITY,
    [Title] nvarchar(max) NULL,
    [Content] nvarchar(max) NULL,
    CONSTRAINT [PK_Posts] PRIMARY KEY ([PostId])
);

CREATE TABLE [Tags] (
    [TagId] nvarchar(450) NOT NULL,
    CONSTRAINT [PK_Tags] PRIMARY KEY ([TagId])
);

CREATE TABLE [PostTag] (
    [PostsId] int NOT NULL,
    [TagsId] nvarchar(450) NOT NULL,
    CONSTRAINT [PK_PostTag] PRIMARY KEY ([PostsId], [TagsId]),
    CONSTRAINT [FK_PostTag_Posts_PostsId] FOREIGN KEY ([PostsId]) REFERENCES [Posts] ([PostId]) ON DELETE CASCADE,
    CONSTRAINT [FK_PostTag_Tags_TagsId] FOREIGN KEY ([TagsId]) REFERENCES [Tags] ([TagId]) ON DELETE CASCADE
);
```

Intern erstellt EF einen Entitätstyp, der die jointabelle darstellt, die als joinentitätstyp bezeichnet wird. `Dictionary<string, object>` wird derzeit verwendet, um eine beliebige Kombination von Fremdschlüssel Eigenschaften zu verarbeiten. Weitere Informationen finden Sie unter [Entitäts Typen für Eigenschaften](shadow-properties.md#property-bag-entity-types) Behälter. Mehrere m:n-Beziehungen können im Modell vorhanden sein. Daher muss dem joinentitätstyp ein eindeutiger Name zugewiesen werden, in diesem Fall `PostTag` . Die Funktion, die dies zulässt, wird als Entitätstyp mit gemeinsamer Typbezeichnung bezeichnet.

> [!IMPORTANT]
> Der CLR-Typ, der für joinentitäts Typen gemäß der Konvention verwendet wird, kann sich in zukünftigen Versionen ändern, um die Leistung Verlassen Sie sich nicht auf den Jointyp `Dictionary<string, object>` , wenn dies nicht explizit konfiguriert wurde, wie im nächsten Abschnitt beschrieben.

Die m:n-Navigationen werden als "Skip Navigationen" bezeichnet, da Sie den joinentitätstyp effektiv überspringen. Wenn Sie die Massen Konfiguration verwenden, können alle Skip-Navigationen aus abgerufen werden <xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType.GetSkipNavigations%2A> .

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToManyShared.cs?name=Metadata)]

#### <a name="join-entity-type-configuration"></a>Join-Entitätstyp Konfiguration

Es kommt häufig vor, dass die Konfiguration auf den Join-Entitätstyp angewendet wird. Diese Aktion kann mithilfe von durchgeführt werden `UsingEntity` .

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToManyShared.cs?name=SharedConfiguration)]

[Modell Ausgangsdaten](xref:core/modeling/data-seeding) können mithilfe anonymer Typen für den Join-Entitätstyp bereitgestellt werden. Sie können die modelldebugansicht überprüfen, um die von der Konvention erstellten Eigenschaftsnamen zu ermitteln.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToManyShared.cs?name=Seeding)]

Zusätzliche Daten können im Join-Entitätstyp gespeichert werden, aber hierfür ist es am besten, einen beständigen CLR-Typ zu erstellen. Beim Konfigurieren der Beziehung mit einem benutzerdefinierten Join-Entitätstyp müssen beide Fremdschlüssel explizit angegeben werden.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToManyPayload.cs?name=ManyToManyPayload)]

#### <a name="joining-relationships-configuration"></a>Beziehungs Konfiguration beitreten

EF verwendet 2 1-zu-viele-Beziehungen für den Join-Entitätstyp, um die m:n-Beziehung darzustellen. Sie können diese Beziehungen in den `UsingEntity` Argumenten konfigurieren.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToManyShared.cs?name=Components)]

> [!NOTE]
> Die Möglichkeit zum Konfigurieren von m:n-Beziehungen wurde in EF Core 5,0 eingeführt. verwenden Sie für die vorherige Version die folgende Vorgehensweise.

#### <a name="indirect-many-to-many-relationships"></a>Indirekte m:n-Beziehungen

Sie können auch eine m:n-Beziehung darstellen, indem Sie einfach den Join-Entitätstyp hinzufügen und zwei separate 1: n-Beziehungen Mapping.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToMany.cs?name=ManyToMany&highlight=16-19,21-24)]

> [!NOTE]
> Der Gerüstbau für m:n-Beziehungen anhand der Datenbank wird noch nicht unterstützt. Siehe [Nachverfolgung von Issue](https://github.com/dotnet/efcore/issues/22475).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [EF Core Community Standup-Sitzung](https://www.youtube.com/watch?v=W1sxepfIMRM&list=PLdo4fOcmZ0oX-DBuRG4u58ZTAJgBAeQ-t&index=32)mit einem detaillierten Einblick in m:n-Beziehungen und der zugrunde liegenden Infrastruktur.
