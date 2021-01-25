---
title: Wiederverwendbare Razor-Benutzeroberfläche in Klassenbibliotheken mit ASP.NET Core
author: Rick-Anderson
description: In diesem Artikel wird erklärt, wie Sie eine wiederverwendbare Razor-Benutzeroberfläche mit Teilansichten in einer Klassenbibliothek in ASP.NET Core erstellen.
ms.author: riande
ms.date: 01/19/2021
ms.custom: mvc, seodec18
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/ui-class
ms.openlocfilehash: a878a3485ecee0782b21ac69c5ec6ff832b9f06c
ms.sourcegitcommit: cb984e0d7dc23a88c3a4121f23acfaea0acbfe1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/19/2021
ms.locfileid: "98571015"
---
# <a name="create-reusable-ui-using-the-no-locrazor-class-library-project-in-aspnet-core"></a>Erstellen einer wiederverwendbaren Benutzeroberfläche mithilfe eines Razor-Klassenbibliotheksprojekts in ASP.NET Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Ansichten, Seiten, Controller, Seitenmodelle, [Razor-Komponenten](xref:blazor/components/class-libraries), [Anzeigekomponenten](xref:mvc/views/view-components) und Datenmodelle im Zusammenhang mit Razor können in einer Razor-Klassenbibliothek (Razor Class Library, RCL) zusammengefasst werden. Die RCL kann verpackt und wiederverwendet werden. Sie kann in Anwendungen eingebunden werden, wodurch sich die in der RCL enthaltenen Ansichten und Seiten überschreiben lassen. Wenn eine Ansicht, Teilansicht oder Razor-Seite sowohl in der Web-App als auch in der RCL enthalten ist, hat das Razor-Markup (die *CSHTML*-Datei) in der Web-App Vorrang.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="create-a-class-library-containing-no-locrazor-ui"></a>Erstellen einer Klassenbibliothek, die eine Razor-Benutzeroberfläche enthält

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klicken Sie in Visual Studio auf **Neues Projekt erstellen**.
* Klicken Sie auf **Razor-Klassenbibliothek** > **Weiter**.
* Benennen Sie die Bibliothek (z. B. RazorClassLib), und klicken Sie auf **Erstellen**. Stellen Sie sicher, dass der Bibliotheksname nicht auf `.Views` endet, um zu verhindern, dass es zu einem Dateinamenkonflikt mit der generierten Ansichtsbibliothek kommt.
* Klicken Sie auf **Support pages and views** (Seiten und Ansichten unterstützen), wenn Ansichten unterstützt werden sollen. Standardmäßig werden nur Razor-Seiten unterstützt. Wählen Sie **Erstellen** aus.

Die Vorlage der Razor-Klassenbibliothek (Razor Class Library, RCL) gilt standardmäßig für die Razor-Komponentenentwicklung. Mit der Option **Support pages and views** (Seiten und Ansichten unterstützen) werden Seiten und Ansichten unterstützt.

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Führen Sie `dotnet new razorclasslib` über die Befehlszeile aus. Zum Beispiel:

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

Die Vorlage der Razor-Klassenbibliothek (Razor Class Library, RCL) gilt standardmäßig für die Razor-Komponentenentwicklung. Übergeben Sie die Option `--support-pages-and-views` (`dotnet new razorclasslib --support-pages-and-views`), damit Seiten und Ansichten unterstützt werden.

Weitere Informationen finden Sie unter [dotnet new](/dotnet/core/tools/dotnet-new). Stellen Sie sicher, dass der Bibliotheksname nicht auf `.Views` endet, um zu verhindern, dass es zu einem Dateinamenkonflikt mit der generierten Ansichtsbibliothek kommt.

---

Fügen Sie der RCL Razor-Dateien zu.

Die ASP.NET Core-Vorlagen gehen davon aus, dass die RCL-Inhalte sich im Ordner *Areas* befinden. Im Abschnitt [RCL-Seitenlayout](#rcl-pages-layout) wird gezeigt, wie Sie eine RCL erstellen, die Inhalte aus `~/Pages` statt aus `~/Areas/Pages` verfügbar macht.

## <a name="reference-rcl-content"></a>Verweisen auf RCL-Inhalte

Folgende Komponenten können auf die RCL verweisen:

* NuGet-Pakete. Weitere Informationen finden Sie unter [Creating NuGet packages](/nuget/create-packages/creating-a-package) (Erstellen von NuGet-Paketen), [dotnet add package](/dotnet/core/tools/dotnet-add-package) und [Erstellen und Veröffentlichen eines NuGet-Pakets](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).
* *{ProjectName}.csproj*-Dateien. Weitere Informationen finden Sie unter [dotnet add reference](/dotnet/core/tools/dotnet-add-reference).

## <a name="override-views-partial-views-and-pages"></a>Überschreiben von Ansichten, Teilansichten und Seiten

Wenn eine Ansicht, Teilansicht oder Razor-Seite sowohl in der Web-App als auch in der RCL enthalten ist, hat das Razor-Markup (die *CSHTML*-Datei) in der Web-App Vorrang. Wenn Sie z. B. *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* zu WebApp1 hinzufügen, hat Page1 in WebApp1 Vorrang vor Page1 in der RCL.

Ob Page1 in WebApp1 tatsächlich die Priorität zugeordnet wird, können Sie testen, indem Sie im Beispieldownload *WebApp1/Areas/MyFeature2* in *WebApp1/Areas/MyFeature* umbenennen.

Kopieren Sie die Teilansicht *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* nach *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*. Aktualisieren Sie das Markup, um den neuen Speicherort anzugeben. Erstellen Sie die App, und führen Sie sie aus, um sicherzustellen, dass die Version mit der Teilansicht der App verwendet wird.

### <a name="rcl-pages-layout"></a>RCL-Seitenlayout

Erstellen Sie ein RCL-Projekt mit der folgenden Dateistruktur, um auf RCL-Inhalte so zu verweisen, als seien diese im Ordner *Pages* der Web-App abgelegt:

* *RazorUIClassLib/Pages*
* *RazorUIClassLib/Pages/Shared*

Nehmen Sie an, dass *RazorUIClassLib/Pages/Shared* zwei Teildateien enthält: *_Header.cshtml* und *_Footer.cshtml*. Die `<partial>`-Tags könnten dann in der Datei *_Layout.cshtml* hinzugefügt werden:

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

Fügen Sie die Datei *_ViewStart.cshtml* dem Ordner *Pages* des RCL-Projekts hinzu, damit die *_Layout.cshtml*-Datei der Web-App des Hosts verwendet wird:

```cshtml
@{
    Layout = "_Layout";
}
```

## <a name="create-an-rcl-with-static-assets"></a>Erstellen einer RCL mit statischen Objekten

Eine RCL kann statische Begleitobjekte erfordern, auf die entweder in der RCL oder in der App, die die RCL verwendet, verwiesen werden kann. ASP.NET Core ermöglicht das Erstellen von RCLs mit statischen Objekten, die verwendenden Apps zur Verfügung stehen.

Wenn Sie Begleitobjekte zu einer RCL hinzufügen möchten, müssen Sie einen Ordner namens *wwwroot* in der Klassenbibliothek erstellen und alle erforderlichen Dateien in diesem Ordner ablegen.

Beim Packen einer RCL werden alle Begleitobjekte im Ordner *wwwroot* automatisch in das Paket gepackt.

Verwenden Sie den `dotnet pack`-Befehl anstelle der NuGet.exe-Version `nuget pack`.

### <a name="exclude-static-assets"></a>Ausschließen statischer Objekte

Fügen Sie den gewünschten Ausschlusspfad zur Eigenschaftengruppe `$(DefaultItemExcludes)` in der Projektdatei hinzu, um statische Objekte auszuschließen. Trennen Sie Einträge durch ein Semikolon (`;`).

Im folgenden Beispiel wird das Stylesheet *lib.css* im Ordner *wwwroot* nicht als statisches Objekt angesehen und nicht in die veröffentlichte RCL aufgenommen:

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a>TypeScript-Integration

Gehen Sie wie folgt vor, um TypeScript-Dateien in eine RCL aufzunehmen:

1. Legen Sie die TypeScript-Dateien ( *.ts*) außerhalb des Ordners *wwwroot* ab. Legen Sie sie zum Beispiel im Ordner *Client* ab.

1. Konfigurieren Sie die TypeScript-Buildausgabe für den Ordner *wwwroot*. Legen Sie die `TypescriptOutDir`-Eigenschaft in einer Eigenschaftengruppe (`PropertyGroup`) wie folgt in der Projektdatei fest:

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. Fügen Sie das TypeScript-Ziel als Abhängigkeit des Ziels `ResolveCurrentProjectStaticWebAssets` hinzu, indem Sie das folgende Ziel in einer Eigenschaftengruppe (`PropertyGroup`) in der Projektdatei hinzufügen:

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a>Inhalte aus einer RCL, auf die verwiesen wird, verwenden

Die Dateien im Ordner *wwwroot* der RCL werden entweder für die RCL oder für die verwendende App unter dem Präfix `_content/{LIBRARY NAME}/` verfügbar gemacht. Für eine Bibliothek mit dem Namen *Razor.Class.Lib* lautet der Pfad zu statischen Inhalten zum Beispiel `_content/Razor.Class.Lib/`. Wenn Sie ein NuGet-Paket erstellen und der Assemblyname nicht der Paket-ID entspricht, verwenden Sie die Paket-ID für `{LIBRARY NAME}`.

Die verwendende App verweist auf statische Objekte, die von der Bibliothek bereitgestellt werden, mit `<script>`, `<style>`, `<img>` und anderen HTML-Tags. Bei der verwendenden App muss [static file support](xref:fundamentals/static-files) (Unterstützung von statischen Dateien) in `Startup.Configure` aktiviert sein:

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

Beim Ausführen der verwendenden App in der Buildausgabe (`dotnet run`) sind statische Webobjekte in der Entwicklungsumgebung standardmäßig aktiviert. Wenn Sie möchten, dass beim Ausführen in der Buildausgabe Objekte in anderen Umgebungen unterstützt werden, rufen Sie im Host-Generator in *Program.cs* `UseStaticWebAssets` auf:

```csharp
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStaticWebAssets();
                webBuilder.UseStartup<Startup>();
            });
}
```

`UseStaticWebAssets` muss nicht aufgerufen werden, wenn eine App in der veröffentlichten Ausgabe ausgeführt wird (`dotnet publish`).

### <a name="multi-project-development-flow"></a>Entwicklungsablauf bei mehreren Projekten

Wenn die verwendende App ausgeführt wird:

* bleiben die Objekte in der RCL in ihren ursprünglichen Ordnern. werden die Objekte nicht in die verwendende App verschoben.
* Änderungen im Ordner *wwwroot* der RCL werden in der verwendenden App abgebildet, sobald die RCL neu erstellt wurde, ohne dass die verwendende App neu erstellt werden muss.

Wenn die RCL erstellt wird, wird ein Manifest erstellt, in dem die Speicherorte der statischen Webobjekte stehen. Die verwendende App liest das Manifest zur Laufzeit, um die Objekte aus den Projekten und Paketen, auf die verwiesen wird, zu verwenden. Wenn ein neues Objekt zu einer RCL hinzugefügt wird, muss die RCL neu erstellt werden, um das Manifest zu aktualisieren, damit verwendende Apps auf das neue Objekt zugreifen können.

### <a name="publish"></a>Veröffentlichen

Wenn die App veröffentlicht wird, werden die Begleitobjekte aus allen Projekten und Paketen, auf die verwiesen wird, in den Ordner *wwwroot* der veröffentlichten App unter `_content/{LIBRARY NAME}/` kopiert.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Ansichten, Seiten, Controller, Seitenmodelle, [Razor-Komponenten](xref:blazor/components/class-libraries), [Anzeigekomponenten](xref:mvc/views/view-components) und Datenmodelle im Zusammenhang mit Razor können in einer Razor-Klassenbibliothek (Razor Class Library, RCL) zusammengefasst werden. Die RCL kann verpackt und wiederverwendet werden. Sie kann in Anwendungen eingebunden werden, wodurch sich die in der RCL enthaltenen Ansichten und Seiten überschreiben lassen. Wenn eine Ansicht, Teilansicht oder Razor-Seite sowohl in der Web-App als auch in der RCL enthalten ist, hat das Razor-Markup (die *CSHTML*-Datei) in der Web-App Vorrang.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="create-a-class-library-containing-no-locrazor-ui"></a>Erstellen einer Klassenbibliothek, die eine Razor-Benutzeroberfläche enthält

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.
* Wählen Sie **ASP.NET Core-Webanwendung** aus.
* Benennen Sie die Bibliothek (z. B. RazorClassLib), und klicken Sie auf **OK**. Stellen Sie sicher, dass der Bibliotheksname nicht auf `.Views` endet, um zu verhindern, dass es zu einem Dateinamenkonflikt mit der generierten Ansichtsbibliothek kommt.
* Überprüfen Sie, ob **ASP.NET Core 2.1** oder höher ausgewählt ist.
* Klicken Sie auf **Razor-Klassenbibliothek** > **OK**.

Eine RCL verfügt über die folgende Projektdatei:

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Führen Sie `dotnet new razorclasslib` über die Befehlszeile aus. Zum Beispiel:

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

Weitere Informationen finden Sie unter [dotnet new](/dotnet/core/tools/dotnet-new). Stellen Sie sicher, dass der Bibliotheksname nicht auf `.Views` endet, um zu verhindern, dass es zu einem Dateinamenkonflikt mit der generierten Ansichtsbibliothek kommt.

---

Fügen Sie der RCL Razor-Dateien zu.

Die ASP.NET Core-Vorlagen gehen davon aus, dass die RCL-Inhalte sich im Ordner *Areas* befinden. Im Abschnitt [RCL-Seitenlayout](#rcl-pages-layout) wird gezeigt, wie Sie eine RCL erstellen, die Inhalte aus `~/Pages` statt aus `~/Areas/Pages` verfügbar macht.

## <a name="reference-rcl-content"></a>Verweisen auf RCL-Inhalte

Folgende Komponenten können auf die RCL verweisen:

* NuGet-Pakete. Weitere Informationen finden Sie unter [Creating NuGet packages](/nuget/create-packages/creating-a-package) (Erstellen von NuGet-Paketen), [dotnet add package](/dotnet/core/tools/dotnet-add-package) und [Erstellen und Veröffentlichen eines NuGet-Pakets](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).
* *{ProjectName}.csproj*-Dateien. Weitere Informationen finden Sie unter [dotnet add reference](/dotnet/core/tools/dotnet-add-reference).

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-no-locrazor-pages-project"></a>Exemplarische Vorgehensweise: Erstellen eines RCL-Projekts und dessen Verwendung in einem Razor Pages-Projekt

Sie können das [vollständige Projekt](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) herunterladen und testen, anstatt es zu erstellen. Der Beispieldownload enthält zusätzlichen Code sowie Links, die das Testen des Projekts erleichtern. [In diesem GitHub-Issue](https://github.com/dotnet/AspNetCore.Docs/issues/6098) können Sie in Form von Kommentaren Feedback zu Unterschieden zwischen Beispieldownloads und ausführlichen Anleitungen geben.

### <a name="test-the-download-app"></a>Testen der heruntergeladenen App

Wenn Sie die vollständige App nicht heruntergeladen haben und das Beispielprojekt selbst erstellen möchten, können Sie mit dem [nächsten Abschnitt](#create-an-rcl) fortfahren.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Öffnen Sie die *SLN*-Datei in Visual Studio. Führen Sie die App aus.

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Erstellen Sie über die Eingabeaufforderung im *cli*-Verzeichnis die RCL und die Web-App.

```dotnetcli
dotnet build
```

Wechseln Sie ins Verzeichnis *WebApp1*, und führen Sie die App aus:

```dotnetcli
dotnet run
```

---

Folgen Sie den Anweisungen in [Testen des WebApp1-Projekts](#test-webapp1).

## <a name="create-an-rcl"></a>Erstellen einer RCL

In diesem Abschnitt wird eine RCL erstellt. Dabei werden der RCL Razor-Dateien hinzugefügt.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Erstellen Sie das RCL-Projekt:

* Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.
* Wählen Sie **ASP.NET Core-Webanwendung** aus.
* Legen Sie als Namen für die App **RazorUIClassLib** fest, und klicken Sie auf **OK**.
* Überprüfen Sie, ob **ASP.NET Core 2.1** oder höher ausgewählt ist.
* Klicken Sie auf **Razor-Klassenbibliothek** > **OK**.
* Fügen Sie eine Razor-Datei für die Teilansicht namens *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* hinzu.

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Führen Sie die folgenden Befehle über die Befehlszeile aus:

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

Die obenstehenden Befehle haben folgende Konsequenzen:

* Die RCL `RazorUIClassLib` wird erstellt.
* Die Razor-Seite „_Message“ wird erstellt und der RCL hinzugefügt. Durch den `-np`-Parameter wird die Seite ohne `PageModel` erstellt.
* Die Datei [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) wird erstellt und der RCL hinzugefügt.

Die Datei *_ViewStart.cshtml* ist erforderlich, um das Layout des Razor Pages-Projekts (das im nächsten Abschnitt hinzugefügt wird) verwenden zu können.

---

### <a name="add-no-locrazor-files-and-folders-to-the-project"></a>Hinzufügen von Razor-Dateien und -Ordnern zum Projekt

* Ersetzen Sie das Markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* durch den folgenden Code:

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* Ersetzen Sie das Markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* durch den folgenden Code:

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` ist erforderlich, um die Teilansicht `<partial name="_Message" />` verwenden zu können. Wenn Sie nicht die `@addTagHelper`-Anweisung einbinden möchten, können Sie die Datei *_ViewImports.cshtml* hinzufügen. Zum Beispiel:

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  Weitere Informationen zu *_ViewImports.cshtml* finden Sie unter [Importieren gemeinsam verwendeter Direktiven](xref:mvc/views/layout#importing-shared-directives).

* Erstellen Sie die Klassenbibliothek, um sicherzustellen, dass keine Compilerfehler vorliegen:

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

Die Buildausgabe enthält *RazorUIClassLib.dll* und *RazorUIClassLib.Views.dll*. *RazorUIClassLib.Views.dll* enthält den kompilierten Razor-Inhalt.

### <a name="use-the-no-locrazor-ui-library-from-a-no-locrazor-pages-project"></a>Verwenden der Razor-Benutzeroberflächenbibliothek über ein Razor Pages-Projekt

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Erstellen Sie die Razor Pages-Web-App:

* Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf die Projektmappe und anschließend auf **Hinzufügen** > **Neues Projekt**.
* Wählen Sie **ASP.NET Core-Webanwendung** aus.
* Legen Sie als Namen für die App **WebApp1** fest.
* Überprüfen Sie, ob **ASP.NET Core 2.1** oder höher ausgewählt ist.
* Klicken Sie auf **Webanwendung** > **OK**.

* Klicken Sie im **Projektmappen-Explorer** zuerst mit der rechten Maustaste auf **WebApp1** und anschließend auf **Als Startprojekt festlegen**.
* Klicken Sie im **Projektmappen-Explorer** zuerst mit der rechten Maustaste auf **WebApp1** und anschließend auf **Buildabhängigkeiten** > **Projektabhängigkeiten**.
* Aktivieren Sie **RazorUIClassLib** als Abhängigkeit von **WebApp1**.
* Klicken Sie im **Projektmappen-Explorer** zuerst mit der rechten Maustaste auf **WebApp1** und anschließend auf **Hinzufügen** > **Verweis**.
* Aktivieren Sie im Dialogfeld Verweis-Manager das Kontrollkästchen neben **UIClassLib, und klicken Sie auf OK.

Führen Sie die App aus.

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Erstellen Sie eine Razor Pages-Web-App und eine Projektmappendatei, die die Razor Pages-App und die RCL enthält:

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

Erstellen Sie die Web-App, und führen Sie sie aus:

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a>Testen des WebApp1-Projekts

Wechseln Sie zu `/MyFeature/Page1`, um zu überprüfen, ob die Klassenbibliothek der Razor-Benutzeroberfläche verwendet wird.

## <a name="override-views-partial-views-and-pages"></a>Überschreiben von Ansichten, Teilansichten und Seiten

Wenn eine Ansicht, Teilansicht oder Razor-Seite sowohl in der Web-App als auch in der RCL enthalten ist, hat das Razor-Markup (die *CSHTML*-Datei) in der Web-App Vorrang. Wenn Sie z. B. *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* zu WebApp1 hinzufügen, hat Page1 in WebApp1 Vorrang vor Page1 in der RCL.

Ob Page1 in WebApp1 tatsächlich die Priorität zugeordnet wird, können Sie testen, indem Sie im Beispieldownload *WebApp1/Areas/MyFeature2* in *WebApp1/Areas/MyFeature* umbenennen.

Kopieren Sie die Teilansicht *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* nach *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*. Aktualisieren Sie das Markup, um den neuen Speicherort anzugeben. Erstellen Sie die App, und führen Sie sie aus, um sicherzustellen, dass die Version mit der Teilansicht der App verwendet wird.

### <a name="rcl-pages-layout"></a>RCL-Seitenlayout

Erstellen Sie ein RCL-Projekt mit der folgenden Dateistruktur, um auf RCL-Inhalte so zu verweisen, als seien diese im Ordner *Pages* der Web-App abgelegt:

* *RazorUIClassLib/Pages*
* *RazorUIClassLib/Pages/Shared*

Nehmen Sie an, dass *RazorUIClassLib/Pages/Shared* zwei Teildateien enthält: *_Header.cshtml* und *_Footer.cshtml*. Die `<partial>`-Tags könnten dann in der Datei *_Layout.cshtml* hinzugefügt werden:

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end

## <a name="additional-resources"></a>Zusätzliche Ressourcen

::: moniker range=">= aspnetcore-5.0"

* <xref:blazor/components/class-libraries>
* <xref:blazor/components/css-isolation#razor-class-library-rcl-support>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <xref:blazor/components/class-libraries>

::: moniker-end
