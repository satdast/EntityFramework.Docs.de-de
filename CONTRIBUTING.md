# <a name="contributing-to-the-entity-framework-documentation"></a>Mitwirken an der Entity Framework-Dokumentation

Nachfolgend wird der Prozess erläutert, wie Sie mit Artikeln und Codebeispielen zur Entity Framework-Dokumentation beitragen können. Die Beiträge können so einfach wie das Korrigieren von Tippfehlern oder so komplex wie das Verfassen neuer Artikel sein.

## <a name="how-to-make-a-simple-correction-or-suggestion"></a>Durchführen einer einfachen Korrektur oder Bereitstellen eines Vorschlags

Artikel werden als Markdowndateien im Repository gespeichert. Um eine einfache Änderung am Inhalt einer Markdowndatei vorzunehmen, klicken Sie auf den Link **Bearbeiten** in der oberen rechten Ecke des Browserfensters. Möglicherweise müssen Sie die Leiste **Optionen** erweitern, um den Link **Bearbeiten** sehen zu können. Befolgen Sie die Anweisungen zum Erstellen eines Pull Requests (PR). Das EF-Team überprüft den PR und akzeptiert diesen oder schlägt Änderungen vor.

## <a name="how-to-make-a-more-complex-submission"></a>So reichen Sie einen komplexeren Vorschlag ein

Sie benötigen Grundkenntnisse zu [Git und GitHub.com](https://guides.github.com/activities/hello-world/).

* Öffnen Sie ein [Problem](https://github.com/dotnet/EntityFramework.Docs/issues/new) (Thema), in dem beschrieben ist, was Sie tun möchten, z. B. Ändern eines vorhandenen Artikels oder Erstellen eines neuen Artikels. Bevor Sie zu viel Zeit investieren, warten Sie auf die Genehmigung des EF-Teams.
* Forken Sie das Repository [dotnet/EntityFramework.Docs](https://github.com/dotnet/EntityFramework.Docs/), und erstellen Sie einen Branch für Ihre Änderungen.
* Senden Sie einen Pull Request (PR) mit Ihren Änderungen an den Master.
* Reagieren Sie auf das PR-Feedback.

## <a name="markdown-syntax"></a>Markdownsyntax

Artikel werden in [DocFx-flavored Markdown (DFM)](http://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html) – eine Erweiterung von [GitHub-flavored Markdown (GFM)](https://guides.github.com/features/mastering-markdown/) – geschrieben. Beispiele zur DFM-Syntax und Metadaten für Features der Benutzeroberfläche, die häufig in der EF-Dokumentation verwendet werden, finden Sie im Styleguide für das .NET Core-Repository unter [Metadaten- und Markdownvorlage](https://github.com/dotnet/docs/blob/master/styleguide/template.md).

## <a name="folder-structure-conventions"></a>Konventionen für Ordnerstrukturen

Bilder und andere statische Inhalte werden in einem `_static`-Ordner in jedem Bereich/Ordner der Website gespeichert.

Codebeispiele werden im Stammordner `samples` gespeichert. Sie werden in einer Ordnerstruktur angeordnet, in der die Dokumentationsstruktur imitiert ist (zu finden unter dem Stammordner `entity-framework`).

## <a name="code-snippets"></a>Codeausschnitte

Artikel enthalten häufig Codeausschnitte, um Argumente zu veranschaulichen. In DFM können Sie Code in die Markdowndatei kopieren oder auf eine separate Codedatei verweisen. Verwenden Sie nach Möglichkeit separate Codedateien, um das Risiko von Fehlern im Code zu minimieren. Die Codedateien müssen im Repository mit der Ordnerstruktur gespeichert werden, die für die obigen Beispielprojekte beschrieben ist.

Es folgen einige Beispiele für die [Syntax von DFM-Codeausschnitten](http://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#code-snippet).

Um eine gesamte Codedatei als Ausschnitt zu rendern, führen Sie Folgendes aus:

```none
[!code-csharp[Main](../../../samples/core/saving/Program.cs)]
```

Um einen Teil einer Datei als Ausschnitt mittels Zeilennummern zu rendern, führen Sie Folgendes aus:

```none
[!code-csharp[Main](../../../samples/core/saving/Program.cs?range=1-10]
```

Bei C#-Ausschnitten können Sie auf eine [C#-Region](https://msdn.microsoft.com/library/9a1ybwek.aspx) verweisen. Verwenden Sie Regionen anstelle von Zeilennummern. Zeilennummern in einer Codedatei ändern sich in der Regel und stimmen dann nicht mehr mit den Zeilennummernverweisen in der Markdowndatei überein. C#-Regionen können geschachtelt werden. Wenn Sie auf die äußere Region verweisen, werden die inneren Anweisungen `#region` und `#endregion` nicht in einem Ausschnitt gerendert.

Um eine C# Region namens „snippet_Example“ zu rendern, führen Sie Folgendes aus:

```none
[!code-csharp[Main](../../../samples/core/saving/Program.cs?name=snippet_Example)]
```

Um ausgewählte Zeilen in einem gerenderten Ausschnitt zu markieren (in der Regel mit gelber Hintergrundfarbe gerendert), führen Sie Folgendes aus:

```none
[!code-csharp[Main](../../../samples/core/saving/Program.cs?name=snippet_Example&highlight=1-3,10,20-25)]
```

## <a name="test-your-changes-with-docfx"></a>Testen Sie Ihre Änderungen mit DocFX

Testen Sie Ihre Änderungen mit dem [DocFX-Befehlszeilentool](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#2-use-docfx-as-a-command-line-tool), wodurch eine lokal gehostete Version der Website erstellt wird. DocFX rendert keine Format- und Websiteerweiterungen, die für „docs.microsoft.com“ erstellt wurden.

DocFX erfordert das .NET Framework für Windows oder Mono für Linux oder macOS.

### <a name="windows-instructions"></a>Anweisungen für Windows

* Laden Sie *docfx.zip* von der Seite [DocFX-Releases](https://github.com/dotnet/docfx/releases) herunter, und entpacken Sie die Datei.
* Fügen Sie DocFX zu Ihrem PATH hinzu.
* Navigieren Sie in einem Befehlszeilenfenster zu dem geklonten Repository (das die Datei *docfx.json* enthält), und führen Sie den folgenden Befehl aus:

   ```console
   docfx -t default --serve
   ```

* Navigieren Sie in einem Browser zu `http://localhost:8080`.

### <a name="mono-instructions"></a>Anweisungen für Mono

* Installieren Sie Mono über Homebrew: `brew install mono`.
* Laden Sie die [aktuelle Version von DocFX](https://github.com/dotnet/docfx/releases/tag/v2.7.2) herunter.
* Extrahieren Sie es unter `\bin\docfx`.
* Erstellen Sie einen Alias für **docfx**:

  ```console
  function docfx {
    mono $HOME/bin/docfx/docfx.exe
  }

  function docfx-serve {
    mono $HOME/bin/docfx/docfx.exe serve _site
  }
  ```

* Führen Sie **docfx** im geklonten Repository aus, um die Website zu erstellen, und **docfx-serve**, um die Website unter `http://localhost:8080` anzuzeigen.

## <a name="voice-and-tone"></a>Sprache und Tonfall

Unser Ziel ist es, Dokumentation bereitzustellen, die für eine größtmögliche Zielgruppe leicht verständlich ist. Zu diesem Zweck haben wir Richtlinien für den Schreibstil entwickelt, um deren Einhaltung wir unsere Beitragenden bitten. Weitere Informationen finden Sie im .NET Core-Repository unter [Voice and tone guidelines](https://github.com/dotnet/docs/blob/master/styleguide/voice-tone.md) (Richtlinien zum Schreibstil).
