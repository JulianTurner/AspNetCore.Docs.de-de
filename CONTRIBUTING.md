# <a name="contribute-to-the-aspnet-core-documentation"></a>Mitwirken an der ASP.NET Core-Dokumentation

In diesem Dokument wird der Prozess bei der Mitwirkung an den Artikeln und Codebeispielen beschrieben, die auf der Website [ASP.NET-Dokumentation](https://docs.microsoft.com/aspnet/) gehostet werden. Willkommen sind Korrekturen von Tippfehlern und neue Artikel.

## <a name="how-to-make-a-simple-correction-or-suggestion"></a>Durchführen einer einfachen Korrektur oder Bereitstellen eines Vorschlags

Artikel werden als Markdowndateien im Repository gespeichert. Einfache Änderungen am Inhalt einer Markdowndatei erfolgen im Browser durch Klicken auf den Link **Bearbeiten** in der oberen rechten Ecke des Browserfensters. (Erweitern Sie in einem zu kleinen Browserfenster die Leiste **Optionen**, um den Link **Bearbeiten** anzuzeigen.) Befolgen Sie die Anweisungen zum Erstellen eines Pull Requests (PR). Wir überprüfen den PR und akzeptieren diesen oder schlagen Änderungen vor.

## <a name="how-to-make-a-more-complex-submission"></a>So reichen Sie einen komplexeren Vorschlag ein

Sie benötigen Grundkenntnisse von [Git und GitHub.com](https://guides.github.com/activities/hello-world/).

* Öffnen Sie ein [Thema](https://github.com/dotnet/AspNetCore.Docs/issues/new), das beschreibt, was Sie tun, z.B. Ändern eines vorhandenen Artikels oder Erstellen eines neuen Artikels. Oftmals bitten wir für einen neuen Themenvorschlag um eine Beschreibung. Bevor Sie zu viel Zeit investieren, warten Sie auf die Genehmigung des Teams.
* Forken Sie das Repository [aspnet/Docs](https://github.com/dotnet/AspNetCore.Docs/), und erstellen Sie einen Branch für Ihre Änderungen.
* Senden Sie einen PR mit Ihren Änderungen an den Master.
* Wenn Ihrem PR die Bezeichnung „cla-required“ zugewiesen ist, [füllen Sie die Lizenzvereinbarung für Mitwirkende (Contribution License Agreement, CLA) aus](https://cla.dotnetfoundation.org/).
* Reagieren Sie auf das PR-Feedback.

Ein Beispiel, in dem dieser Prozess zur Veröffentlichung eines neuen Artikels geführt hat, finden Sie im Repository für die .NET-Dokumentation im [Thema &num;67](https://github.com/dotnet/docs/issues/67) und im [Pull Request &num;798](https://github.com/dotnet/docs/pull/798). Der neue Artikel lautet [Dokumentieren von Code mit XML-Kommentaren](https://docs.microsoft.com/dotnet/articles/csharp/codedoc).

## <a name="docs-authoring-pack-extension-in-visual-studio-code"></a>Docs Authoring Pack-Erweiterung in Visual Studio Code

Wenn Sie über Visual Studio Code an der ASP.NET-Dokumentation mitwirken, können Sie Ihre Produktivität durch die Installation der Erweiterung [Docs Authoring Pack](https://marketplace.visualstudio.com/items?itemName=docsmsft.docs-authoring-pack) steigern. Diese Erweiterung bietet eine Vielzahl von Tools, die beim Linten von Markdowndateien, der Rechtschreibprüfung des Codes und bei Artikelvorlagen unterstützen.

## <a name="markdown-syntax"></a>Markdownsyntax

Artikel werden in [DocFx flavored Markdown](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html) – eine Erweiterung von [GitHub-flavored Markdown (GFM)](https://guides.github.com/features/mastering-markdown/) – geschrieben. Beispiele zur DFM-Syntax für Features der Benutzeroberfläche, die häufig in der ASP.NET-Dokumentation verwendet werden, finden Sie im Styleguide für das .NET-Dokumentationsrepository unter [Metadaten- und Markdownvorlage](https://github.com/dotnet/docs/blob/master/styleguide/template.md). 

## <a name="folder-structure-conventions"></a>Konventionen für Ordnerstrukturen

Für jede Markdowndatei ist eventuell ein Ordner für Bilder und ein Ordner für Beispielcode vorhanden. Wenn sich der Artikel in der Datei [fundamentals/configuration/index.md](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/fundamentals/configuration/index.md) befindet, befinden sich die Bilder unter [fundamentals/configuration/index/\_static](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/_static) und die Projektdateien für die Beispiel-App unter [ fundamentals/configuration/index/sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/sample). Ein Bild in der Datei *fundamentals/configuration/index.md* wird von der folgenden Markdowndatei gerendert:

```md
![description of image for alt attribute](configuration/index/_static/imagename.png)
```

Alle Bilder müssen mit [Alternativtext (alt)](https://wikipedia.org/wiki/Alt_attribute) versehen sein. Hinweise zum Angeben von Alternativtext finden Sie in den Onlineressourcen, z.B. auf der WebAIM-Website unter [Alternative Text](https://webaim.org/techniques/alttext/) (Alternativtext).

Verwenden Sie Kleinbuchstaben für Namen von Markdown- und Bilddateien.

## <a name="internal-links"></a>Interne Links

Interne Links sollten die `uid` des Zielartikels mit einer xref-Verknüpfung enthalten (Linktext ist auf den Titel des verknüpften Inhalts festgelegt):

```md
<xref:uid_of_the_topic>
```

Wenn der Titel des Artikels für den Linktext ungeeignet ist (z.B. ein Wort oder ein Ausdruck in einem Satz), geben Sie die xref-Verknüpfung und den Linktext mit Folgendem an:

```md
[link text](xref:uid_of_the_topic)
```

Weitere Informationen finden Sie auf der DocFX-Website unter [Cross Reference](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#cross-reference) (Querverweis).

## <a name="images-and-screenshots"></a>Bilder und Screenshots

Fügen Sie nur in den folgenden Fällen Bilder in Artikel ein:

* In grundlegenden Tutorials für Anfänger
* Wenn ein Bild für die Übersichtlichkeit erforderlich ist

Diese Einschränkungen sollen die Repositorygröße auf einem Minimum halten.

Stellen Sie optional sicher, dass alle Bilder und Screenshots, die in der Dokumentation verwendet werden, komprimiert sind. Dies reduziert die Dateigröße und erhöht die Leistung beim Laden von Seiten. Zu den gängigen Tools zählen TinyPNG (mit der [TinyPNG-Website](https://tinypng.com/) oder der [TinyPNG-API](https://tinypng.com/developers)) oder die [Image Optimizer](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.ImageOptimizer)-Erweiterung für Visual Studio. 

## <a name="code-snippets"></a>Codeausschnitte

Artikel enthalten häufig Codeausschnitte, um Argumente zu veranschaulichen. Mithilfe von DFM können Sie Code in der Markdowndatei kopieren oder auf eine separate Codedatei verweisen. Wir bevorzugen möglichst die Verwendung separater Codedateien, um das Risiko von Fehlern im Code zu minimieren. Die Codedateien werden im Repository mit der Ordnerstruktur gespeichert, die für die Beispielprojekte zuvor beschrieben wurde. 

Die folgenden Beispiele veranschaulichen die [Syntax des DFM-Codeausschnitts](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#code-snippet) für die Verwendung in der Datei *configuration/index.md*.

Um eine gesamte Codedatei als Ausschnitt zu rendern, führen Sie Folgendes aus:

```md
[!code-csharp[](configuration/index/sample/Program.cs)]
```

Um einen Teil einer Datei als Ausschnitt mittels Zeilennummern zu rendern, führen Sie Folgendes aus:

```md
[!code-csharp[](configuration/index/sample/Program.cs?range=1-10,20,30,40-50]
[!code-html[](configuration/index/sample/Views/Home/Index.cshtml?range=1-10,20,30,40-50]
```

Informationen zu C#-Ausschnitten finden Sie in der Referenz für [C#-Regionen](https://docs.microsoft.com/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region). Verwenden Sie nach Möglichkeit Regionen anstelle von Zeilennummern, da sich Zeilennummern in einer Codedatei in der Regel ändern und nicht mehr den Zeilennummernverweisen in der Markdowndatei entsprechen können. C#-Regionen können geschachtelt werden. Wenn auf die äußere Region verwiesen wird, werden die inneren `#region`- und `#endregion`-Anweisungen in einem Ausschnitt nicht gerendert. 

Um eine C# Region namens „snippet_Example“ zu rendern, führen Sie Folgendes aus:

```md
[!code-csharp[](configuration/index/sample/Program.cs?name=snippet_Example)]
```

Um ausgewählte Zeilen in einem gerenderten Ausschnitt zu markieren (in der Regel mit gelber Hintergrundfarbe gerendert), führen Sie Folgendes aus:

```md
[!code-csharp[](configuration/index/sample/Program.cs?name=snippet_Example&highlight=1-3,10,20-25)]
[!code-csharp[](configuration/index/sample/Program.cs?range=10-20&highlight=1-3]
[!code-html[](configuration/index/sample/Views/Home/Index.cshtml?range=10-20&highlight=1-3]
[!code-javascript[](configuration/index/sample/UsingOptionsSample.csproj?range=10-20&highlight=1-3]
```

## <a name="test-changes-with-docfx"></a>Testen von Änderungen mit DocFX

Testen Sie Ihre Änderungen mit dem [DocFX-Befehlszeilentool](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#2-use-docfx-as-a-command-line-tool), wodurch eine lokal gehostete Version der Website erstellt wird. DocFX rendert keine Format- und Websiteerweiterungen, die für „docs.microsoft.com“ erstellt wurden.

Für DocFX ist Folgendes erforderlich:

* .NET Framework unter Windows
* Mono für Linux oder macOS 

### <a name="windows-instructions"></a>Anweisungen für Windows

* Laden Sie *docfx.zip* von der Seite [DocFX-Releases](https://github.com/dotnet/docfx/releases) herunter, und entpacken Sie die Datei.
* Fügen Sie DocFX zu Ihrem PATH hinzu.
* Navigieren Sie in einer Befehlsshell zu dem Ordner, der die Datei *docfx.json* enthält (*aspnet* für ASP.NET-Inhalt oder *aspnetcore* für ASP.NET Core-Inhalt), und führen Sie den folgenden Befehl aus:

  ```console
  docfx --serve
  ```

* Navigieren Sie in einem Browser zu `http://localhost:8080/group1-dest/`.

### <a name="mono-instructions"></a>Anweisungen für Mono

* Installieren Sie Mono über Homebrew:

  ```console
  brew install mono
  ```

* Laden Sie die [aktuelle Version von DocFX](https://github.com/dotnet/docfx/releases) herunter.
* Extrahieren Sie das Archiv zu *$HOME/bin/docfx*.
* Erstellen Sie ein Paar von Aliasen für **docfx** in einer Bash-Shell. Der erste Alias wird verwendet, um die Dokumentation zu erstellen. Der zweite Alias wird verwendet, um die Dokumentation zu erstellen und sie zu bedienen.

  ```console
  alias docfx='mono $HOME/bin/docfx/docfx.exe'
  alias docfx-serve='mono $HOME/bin/docfx/docfx.exe --serve'
  ```

* Navigieren Sie in einer Befehlsshell zu dem Ordner, der die Datei *docfx.json* enthält (*aspnet* für ASP.NET-Inhalt oder *aspnetcore* für ASP.NET Core-Inhalt), und führen Sie den folgenden Befehl aus, um die Dokumente über ihren Alias zu erstellen und zu bedienen:

  ```console
  docfx-serve
  ```

* Navigieren Sie in einem Browser zu `http://localhost:8080/group1-dest/`.

## <a name="voice-and-tone"></a>Sprache und Tonfall

Unser Ziel ist es, Dokumentation bereitzustellen, die für eine größtmögliche Zielgruppe leicht verständlich ist. Zu diesem Zweck haben wir Richtlinien für den Schreibstil entwickelt, um deren Einhaltung wir unsere Beitragende bitten. Weitere Informationen finden Sie im .NET-Repository unter [Richtlinien zum Schreibstil](https://github.com/dotnet/docs/blob/master/styleguide/voice-tone.md).

## <a name="microsoft-writing-style-guide"></a>Microsoft-Styleguide für den Schreibstil

Der [Microsoft-Styleguide für den Schreibstil](https://docs.microsoft.com/style-guide/welcome/) enthält Leitlinien für den Schreibstil und die Terminologie für alle Formen von technischer Dokumentation, einschließlich der ASP.NET Core-Dokumentation.

## <a name="redirects"></a>Umleitungen

Wenn Sie einen Artikel löschen, ändern Sie den Dateinamen, oder in einen anderen Ordner verschieben, erstellen Sie eine Umleitung, damit Benutzer, die den Artikel als Lesezeichen gespeichert haben, nicht die Fehlermeldung *404 Nicht gefunden* erhalten. Fügen Sie Umleitungen zur [Masterumleitungsdatei](https://github.com/dotnet/AspNetCore.Docs/blob/master/.openpublishing.redirection.json) hinzu.
