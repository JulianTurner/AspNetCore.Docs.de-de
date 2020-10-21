---
title: Weitere Informationen zum Migrieren von ASP.NET MVC zu ASP.net Core MVC
author: wadepickett
description: Erfahren Sie, wie Sie mit dem Migrieren eines ASP.NET MVC-Projekts zu ASP.net Core MVC beginnen.
ms.author: wpickett
ms.date: 06/18/2020
no-loc:
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
uid: migration/mvc
ms.openlocfilehash: 51228e59284b5edf0554e9929b16deafe08ea31e
ms.sourcegitcommit: b5ebaf42422205d212e3dade93fcefcf7f16db39
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/21/2020
ms.locfileid: "92326626"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a>Migrating from ASP.NET MVC to ASP.NET Core MVC (Migrieren von ASP.NET MVC zu ASP.NET Core MVC)

::: moniker range=">= aspnetcore-3.0"

In diesem Artikel wird gezeigt, wie Sie mit dem Migrieren eines ASP.NET MVC-Projekts zu [ASP.net Core MVC](xref:mvc/overview)beginnen. Im Prozess werden verwandte Änderungen von ASP.NET MVC hervorgehoben.

Die Migration von ASP.NET MVC ist ein mehrstufiger Prozess. In diesem Artikel wird Folgendes behandelt:

* Anfängliche Einrichtung.
* Grundlegende Controller und Ansichten.
* Statischer Inhalt.
* Client seitige Abhängigkeiten.

Informationen zum Migrieren von Konfiguration und Identity Code finden [Sie unter Migrieren der Konfiguration zu ASP.net Core](xref:migration/configuration) und [Migrieren der Authentifizierung und Identity ASP.net Core](xref:migration/identity).

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="create-the-starter-aspnet-mvc-project"></a>Erstellen des MVC-Projekts Starter ASP.net

Erstellen Sie ein Beispiel ASP.NET MVC-Projekt in Visual Studio für die Migration:

1. Klicken Sie im Menü **Datei** auf **Neu** > **Projekt**.
1. Wählen Sie **ASP.NET-Webanwendung (.NET Framework)** , und klicken Sie dann auf **weiter**.
1. Benennen Sie das Projekt *"WebApp1"* damit der Namespace mit dem im nächsten Schritt erstellten ASP.net Core Projekt übereinstimmt. Klicken Sie auf **Erstellen**.
1. Wählen Sie **MVC**aus, und wählen Sie dann **Erstellen**aus.

## <a name="create-the-aspnet-core-project"></a>Erstellen des ASP.net Core Projekts

Erstellen Sie eine neue Projekt Mappe mit einem neuen ASP.net Core Projekt, zu dem migriert werden soll:

1. Starten Sie eine zweite Instanz von Visual Studio.
1. Klicken Sie im Menü **Datei** auf **Neu** > **Projekt**.
1. Klicken Sie auf **ASP.NET Core-Webanwendung** und dann auf **Weiter**.
1. Benennen Sie im Dialogfeld **Neues Projekt konfigurieren** das Projekt *"WebApp1"*.
1. Legen Sie den Speicherort auf ein anderes Verzeichnis als das vorherige Projekt fest, um denselben Projektnamen zu verwenden. Wenn Sie den gleichen Namespace verwenden, ist es einfacher, Code zwischen den beiden Projekten zu kopieren. Klicken Sie auf **Erstellen**.
1. Vergewissern Sie sich, dass im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Optionen **.NET Core** und **ASP.NET Core 3.1** ausgewählt sind. Wählen Sie die Projektvorlage **Webanwendung (Model-View-Controller)** aus, und klicken Sie auf **Erstellen**.

## <a name="configure-the-aspnet-core-site-to-use-mvc"></a>Konfigurieren des ASP.net Core-Standorts für die Verwendung von MVC

In ASP.net Core 3,0-und späteren Projekten wird .NET Framework nicht mehr als Ziel Framework unterstützt. Ihr Projekt muss auf .net Core ausgerichtet sein. Die ASP.net Core Shared Framework, das MVC umfasst, ist Teil der .net Core-Lauf Zeit Installation. Bei Verwendung des `Microsoft.NET.Sdk.Web` SDK in der Projektdatei wird automatisch auf das freigegebene Framework verwiesen:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Weitere Informationen finden Sie unter [frameworkreferenz](xref:migration/22-to-30#framework-reference).

In ASP.net Core die- `Startup` Klasse:

* Ersetzt *Global. asax*.
* Behandelt alle App-Start Tasks.

Weitere Informationen finden Sie unter <xref:fundamentals/startup>.

Öffnen Sie im Projekt ASP.net Core die Datei *Startup.cs* :

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=13,30,32&name=snippet)]

ASP.net Core-apps müssen Frameworkfunktionen mit Middleware abonnieren. Der vorherige von der Vorlage generierte Code fügt die folgenden Dienste und Middleware hinzu:

* Die <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> Erweiterungsmethode registriert die MVC-Dienst Unterstützung für Controller, API-bezogene Features und Sichten. Weitere Informationen zu MVC-Dienst Registrierungs Optionen finden Sie unter [MVC-Dienst Registrierung](xref:migration/22-to-30#mvc-service-registration) .
* Die <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> Erweiterungsmethode fügt den statischen Datei Handler hinzu `Microsoft.AspNetCore.StaticFiles` . Die `UseStaticFiles` Erweiterungsmethode muss vor aufgerufen werden `UseRouting` . Weitere Informationen finden Sie unter <xref:fundamentals/static-files>.
* Die <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> Erweiterungsmethode fügt Routing hinzu. Weitere Informationen finden Sie unter <xref:fundamentals/routing>.

Diese vorhandene Konfiguration umfasst das, was zum Migrieren des MVC-Beispielprojekts erforderlich ist. Weitere Informationen zu ASP.net Core Middleware-Optionen finden Sie unter <xref:fundamentals/startup> .

## <a name="migrate-controllers-and-views"></a>Migrieren von Controllern und Ansichten

Im ASP.net Core Projekt wird eine neue leere Controller Klasse und Ansichts Klasse hinzugefügt, um als Platzhalter zu fungieren, die dieselben Namen wie die Controller-und Ansichts Klassen in jedem ASP.NET MVC-Projekt für die Migration verwenden.

Das ASP.net Core *"WebApp1"* -Projekt enthält bereits einen minimalen Beispiel Controller und eine Ansicht mit dem gleichen Namen wie das ASP.NET MVC-Projekt. Diese dienen als Platzhalter für den ASP.NET MVC-Controller und Sichten, die aus dem ASP.NET MVC *"WebApp1"* -Projekt migriert werden sollen.

1. Kopieren Sie die Methoden aus ASP.NET MVC `HomeController` , um die neuen ASP.net Core `HomeController` Methoden zu ersetzen. Es ist nicht erforderlich, den Rückgabetyp der Aktionsmethoden zu ändern. Der Rückgabetyp der Controller Aktionsmethode der integrierten ASP.NET MVC-Vorlage ist <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> ; in ASP.net Core MVC geben die Aktionsmethoden `IActionResult` stattdessen zurück. `ActionResult` implementiert `IActionResult`.
1. Klicken Sie im ASP.net Core-Projekt mit der rechten Maustaste auf das Verzeichnis *views/Home* , und wählen Sie **Add** > **Vorhandenes Element**hinzufügen aus.
1. Navigieren Sie im Dialogfeld **Vorhandenes Element hinzufügen** zum Verzeichnis *views/Home* des ASP.NET MVC *"WebApp1"* -Projekts.
1. Wählen Sie die Ansichts Dateien *about. cshtml*, *Contact. cshtml*und *Index. cshtml* aus Razor , und klicken Sie dann auf **Hinzufügen**, um die vorhandenen Dateien zu ersetzen.

Weitere Informationen finden Sie unter <xref:mvc/controllers/actions> und <xref:mvc/views/overview>.

## <a name="test-each-method"></a>Jede Methode testen

Jeder Controller Endpunkt kann getestet werden, aber Layout und Stile werden später im Dokument behandelt.

1. Führen Sie die ASP.net Core-App aus.
1. Rufen Sie die gerenderten Sichten aus dem Browser auf der laufenden ASP.net Core-App auf, indem Sie die aktuelle Portnummer durch die im ASP.net Core Projekt verwendete Portnummer ersetzen. Beispiel: `https://localhost:44375/home/about`.

## <a name="migrate-static-content"></a>Migrieren statischer Inhalte

In ASP.NET MVC 5 und früheren Versionen wurde statischer Inhalt aus dem Stammverzeichnis des Webprojekts gehostet und mit serverseitigen Dateien gemischt. In ASP.net Core werden statische Dateien im [Webstamm](xref:fundamentals/index#web-root) Verzeichnis des Projekts gespeichert. Das Standardverzeichnis ist *{Content root}/wwwroot*, kann jedoch geändert werden. Weitere Informationen finden Sie unter [Statische Dateien in ASP.NET Core](xref:fundamentals/static-files#serve-static-files).

Kopieren Sie den statischen Inhalt aus dem ASP.NET MVC *"WebApp1"* -Projekt in das Verzeichnis " *wwwroot* " im Projekt "ASP.net Core *" WebApp1 "* ":

1. Klicken Sie im ASP.net Core-Projekt mit der rechten Maustaste auf das Verzeichnis *wwwroot* , und wählen Sie **Add** > **Vorhandenes Element**hinzufügen aus.
1. Navigieren Sie im Dialogfeld **Vorhandenes Element hinzufügen** zum ASP.NET MVC *"WebApp1"* -Projekt.
1. Wählen Sie die Datei *Favicon. ico aus* , und klicken Sie auf **Hinzufügen**, um die vorhandene Datei zu ersetzen.

## <a name="migrate-the-layout-files"></a>Migrieren der Layoutdateien

Kopieren Sie die ASP.NET MVC-projektlayoutdateien in das ASP.net Core Projekt:

1. Klicken Sie im ASP.net Core Projekt mit der rechten Maustaste auf das Verzeichnis *Sichten* , und wählen Sie **Add** > **Vorhandenes Element**hinzufügen aus.
1. Navigieren Sie im Dialogfeld **Vorhandenes Element hinzufügen** zum Verzeichnis *views* -Verzeichnis des ASP.NET MVC *"WebApp1"* -Projekts.
1. Wählen Sie die Datei *_ViewStart. cshtml* und dann **Hinzufügen**aus.

Kopieren Sie die freigegebenen Layoutdateien des ASP.NET-MVC-Projekts in das ASP.net Core Projekt:

1. Klicken Sie im ASP.net Core-Projekt mit der rechten Maustaste auf das Verzeichnis *views/Shared* , und wählen Sie **Add** > **Vorhandenes Element**hinzufügen aus.
1. Navigieren Sie im Dialogfeld **Vorhandenes Element hinzufügen** zum Verzeichnis *views/Shared* des ASP.NET MVC *"WebApp1"* -Projekts.
1. Wählen Sie die Datei *_Layout. cshtml* aus, und klicken Sie dann auf **Hinzufügen**, um die vorhandene Datei zu ersetzen.

Öffnen Sie im Projekt ASP.net Core die Datei *_Layout. cshtml* . Nehmen Sie die folgenden Änderungen vor, um den unten gezeigten vollständigen Code abzugleichen:

Aktualisieren Sie die Bootstrap-CSS-Einbindung, damit Sie mit dem unten stehenden Code identisch ist

1. Ersetzen `@Styles.Render("~/Content/css")` Sie dies durch ein- `<link>` Element, das *Bootstrap. CSS* laden soll (siehe unten).
1. Entfernen Sie `@Scripts.Render("~/bundles/modernizr")`.

Das abgeschlossene Ersetzungs Markup für die Bootstrap-CSS-Einbindung:

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

Aktualisieren Sie die jQuery-und Bootstrap-JavaScript-Einbindung entsprechend dem unten stehenden Code

1. Ersetzen Sie dies `@Scripts.Render("~/bundles/jquery")` durch ein- `<script>` Element (siehe unten).
1. Ersetzen Sie dies `@Scripts.Render("~/bundles/bootstrap")` durch ein- `<script>` Element (siehe unten).

Das abgeschlossene Ersetzungs Markup für die jQuery-und Bootstrap-JavaScript-Einbindung:

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

Die aktualisierte Datei *_Layout. cshtml* ist unten dargestellt:

[!code-cshtml[](mvc/samples/3.x/Views/Shared/_Layout.cshtml?highlight=7-10,40-42)]

Zeigen Sie die Website im Browser an. Er sollte mit den erwarteten Stilen dargestellt werden.

## <a name="configure-bundling-and-minification"></a>Konfigurieren der Bündelung und Minimierung

ASP.net Core ist mit mehreren Open-Source-bündeln und [minisierungslösungen wie weboptimizer](https://github.com/ligershark/WebOptimizer) und anderen ähnlichen Bibliotheken kompatibel. ASP.net Core bietet keine native Bündelung und Minimierung. Weitere Informationen zum Konfigurieren von Bündelung und Minimierung finden Sie unter [Bündelung und Minimierung](xref:client-side/bundling-and-minification).

## <a name="solve-http-500-errors"></a>Beheben von HTTP 500-Fehlern

Es gibt viele Probleme, die eine HTTP 500-Fehlermeldung verursachen können, die keine Informationen zur Ursache des Problems enthält. Wenn die Datei *views/_ViewImports. cshtml* beispielsweise einen Namespace enthält, der im Projekt nicht vorhanden ist, wird ein HTTP 500-Fehler generiert. Standardmäßig wird die Erweiterung in ASP.net Core `UseDeveloperExceptionPage` -apps hinzugefügt `IApplicationBuilder` und bei der *Entwicklung*der Umgebung ausgeführt. Dies wird im folgenden Code ausführlich erläutert:

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=17-21&name=snippet)]

ASP.net Core konvertiert nicht behandelte Ausnahmen in HTTP 500-Fehler Antworten. Normalerweise werden Fehlerdetails nicht in diese Antworten eingeschlossen, um das Offenlegen von potenziell sensiblen Informationen über den Server zu verhindern. Weitere Informationen finden Sie auf der [Seite Entwickler Ausnahme](xref:fundamentals/error-handling#developer-exception-page).

## <a name="next-steps"></a>Nächste Schritte

* <xref:migration/identity>

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

In diesem Artikel wird gezeigt, wie Sie mit der Migration eines ASP.NET MVC-Projekts zu [ASP.net Core MVC](xref:mvc/overview) 2,2 beginnen. In diesem Prozess werden viele der Dinge hervorgehoben, die sich seit ASP.NET MVC geändert haben. Die Migration von ASP.NET MVC ist ein mehrstufiger Prozess. In diesem Artikel wird Folgendes behandelt:

* Erste Einrichtung
* Grundlegende Controller und Ansichten
* Statischer Inhalt
* Client seitige Abhängigkeiten.

Informationen zum Migrieren von Konfiguration und Identity Code finden Sie unter <xref:migration/configuration> und <xref:migration/identity> .

> [!NOTE]
> Die Versionsnummern in den Beispielen sind möglicherweise nicht aktuell, aktualisieren Sie die Projekte entsprechend.

## <a name="create-the-starter-aspnet-mvc-project"></a>Erstellen des MVC-Projekts Starter ASP.net

Um das Upgrade zu veranschaulichen, erstellen wir zunächst eine ASP.NET MVC-app. Erstellen Sie die Datei mit dem Namen *"WebApp1"* , damit der Namespace mit dem im nächsten Schritt erstellten ASP.net Core Projekt übereinstimmt.

![Visual Studio - Dialogfeld „Neues Projekt“](mvc/_static/new-project.png)

![Dialogfeld "neue Webanwendung": MVC-Projektvorlage im Bereich "ASP.net Templates"](mvc/_static/new-project-select-mvc-template.png)

*Optional:* Ändern Sie den Namen der Projekt Mappe von *"WebApp1"* in *Mvc5*. Visual Studio zeigt den neuen Projektmappennamen an (*Mvc5*). Dadurch wird das Projekt leichter vom nächsten Projekt aus informiert.

## <a name="create-the-aspnet-core-project"></a>Erstellen des ASP.net Core Projekts

Erstellen Sie eine neue *leere* ASP.net Core Web-App mit dem gleichen Namen wie das vorherige Projekt (*"WebApp1"*), damit die Namespaces in den beiden Projekten übereinstimmen. Wenn Sie den gleichen Namespace haben, ist es einfacher, Code zwischen den beiden Projekten zu kopieren. Erstellen Sie dieses Projekt in einem anderen Verzeichnis als das vorherige Projekt, um denselben Namen zu verwenden.

![Dialogfeld "Neues Projekt"](mvc/_static/new_core.png)

![Dialogfeld "neue ASP.NET Webanwendung": leere Projektvorlage im Bereich "ASP.net Core Vorlagen" ausgewählt](mvc/_static/new-project-select-empty-aspnet5-template.png)

* *Optional:* Erstellen Sie mithilfe der Projektvorlage für die *Webanwendung* eine neue ASP.net Core-app. Nennen Sie das Projekt *"WebApp1"*, und wählen Sie eine Authentifizierungs Option **einzelner Benutzerkonten**aus. Benennen Sie diese APP in *fullaspnetcore*um. Wenn Sie dieses Projekt erstellen, sparen Sie Zeit in der Konvertierung. Das Endergebnis kann im von der Vorlage generierten Code angezeigt werden, Code kann in das Konvertierungs Projekt kopiert oder mit dem Vorlagen generierten Projekt verglichen werden.

## <a name="configure-the-site-to-use-mvc"></a>Konfigurieren des Standorts für die Verwendung von MVC

* Wenn .net Core als Ziel verwendet wird, wird standardmäßig auf das [Metapaket Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app) verwiesen. Dieses Paket enthält Pakete, die häufig von MVC-Apps verwendet werden. Wenn Sie .NET Framework adressieren, müssen Paket Verweise einzeln in der Projektdatei aufgelistet werden.

`Microsoft.AspNetCore.Mvc` ist das ASP.net Core MVC-Framework. `Microsoft.AspNetCore.StaticFiles` ist der statische Datei Handler. ASP.net Core-apps entscheiden sich explizit für Middleware, z. b. für die Betreuung statischer Dateien. Weitere Informationen finden Sie im Artikel zu [statischen Dateien](xref:fundamentals/static-files).

* Öffnen Sie die Datei *Startup.cs* , und ändern Sie den Code so, dass er dem folgenden entspricht:

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

Die <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> Erweiterungsmethode fügt den statischen Datei Handler hinzu. Weitere Informationen finden Sie unter [starten](xref:fundamentals/startup) und [Routing](xref:fundamentals/routing)von Anwendungen.

## <a name="add-a-controller-and-view"></a>Controller und Ansicht hinzufügen

In diesem Abschnitt werden ein minimaler Controller und eine Ansicht hinzugefügt, die als Platzhalter für den ASP.NET MVC-Controller und im nächsten Abschnitt migrierte Sichten fungieren.

* Fügen Sie ein *Controller* Verzeichnis hinzu.

* Fügen Sie dem Verzeichnis *Controller* eine **Controller Klasse** mit dem Namen *HomeController.cs* hinzu.

![Dialogfeld „Neues Element hinzufügen“](mvc/_static/add_mvc_ctl.png)

* Fügen Sie ein *views* -Verzeichnis hinzu.

* Fügen Sie ein *views/Home-* Verzeichnis hinzu.

* Fügen Sie dem Verzeichnis *views/Home* eine ** Razor Ansicht** mit dem Namen *Index. cshtml* hinzu.

![Dialogfeld „Neues Element hinzufügen“](mvc/_static/view.png)

Die Projektstruktur wird unten dargestellt:

![Projektmappen-Explorer anzeigen von Dateien und Verzeichnissen von "WebApp1"](mvc/_static/project-structure-controller-view.png)

Ersetzen Sie die Inhalte der Datei *Views/HelloWorld/Index.cshtml* durch das folgende Markup:

```html
<h1>Hello world!</h1>
```

Führen Sie die App aus.

![Die Web-App wird in Microsoft Edge geöffnet.](mvc/_static/hello-world.png)

Weitere Informationen finden Sie unter [Controller](xref:mvc/controllers/actions) und [Ansichten](xref:mvc/views/overview).

Die folgende Funktionalität erfordert eine Migration von dem Beispiel ASP.NET MVC-Projekt zum ASP.net Core Projekt:

* Client seitiger Inhalt (CSS, Schriftarten und Skripts)

* Controller

* views

* Modelle

* anbietet

* Filter

* Melden Sie sich an/aus Identity (Dies erfolgt im nächsten Tutorial).

## <a name="controllers-and-views"></a>Controller und Ansichten

* Kopieren Sie jede Methode aus ASP.NET MVC `HomeController` in die neue `HomeController` . In ASP.NET MVC lautet der Rückgabetyp der Controller Aktionsmethode der integrierten Vorlage <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> ; in ASP.net Core MVC geben die Aktionsmethoden `IActionResult` stattdessen zurück. `ActionResult` implementiert `IActionResult` , sodass es nicht erforderlich ist, den Rückgabetyp der Aktionsmethoden zu ändern.

* Kopieren Sie die Ansichts Dateien " *about. cshtml*", " *Contact. cshtml*" und " *Index. cshtml* " Razor vom ASP.NET-MVC-Projekt in das ASP.net Core-Projekt.

## <a name="test-each-method"></a>Jede Methode testen

Die Layoutdatei und-Stile wurden noch nicht migriert, sodass die gerenderten Sichten nur den Inhalt der Ansichts Dateien enthalten. Die von der Layoutdatei generierten `About` `Contact` Verknüpfungen für die Ansichten und sind noch nicht verfügbar.

Rufen Sie die gerenderten Sichten aus dem Browser auf der laufenden ASP.net Core-App auf, indem Sie die aktuelle Portnummer durch die im ASP.net Core-Projekt verwendete Portnummer ersetzen. Beispiel: `https://localhost:44375/home/about`.

![Kontaktseite](mvc/_static/contact-page.png)

Beachten Sie den Mangel an Formatierungs-und Menü Elementen. Die Formatierung wird im nächsten Abschnitt korrigiert.

## <a name="static-content"></a>Statischer Inhalt

In ASP.NET MVC 5 und früheren Versionen wurde statischer Inhalt aus dem Stammverzeichnis des Webprojekts gehostet und mit serverseitigen Dateien gemischt. In ASP.net Core wird statischer Inhalt im Verzeichnis " *wwwroot* " gehostet. Kopieren Sie den statischen Inhalt aus der ASP.NET MVC-app in das Verzeichnis " *wwwroot* " im ASP.net Core-Projekt. In dieser Beispiel Konvertierung:

* Kopieren Sie die Datei " *Favicon. ico* " aus dem ASP.NET-MVC-Projekt in das Verzeichnis " *wwwroot* " im ASP.net Core-Projekt.

Das ASP.NET MVC-Projekt verwendet [Bootstrap](https://getbootstrap.com/) für seine Formatierung und speichert die Bootstrap-Dateien in den Verzeichnissen " *Content* " und " *Scripts* ". Die Vorlage, die das ASP.NET MVC-Projekt generiert hat, verweist auf Bootstrap in der Layoutdatei (*views/Shared/_Layout. cshtml*). Die Dateien " *bootstrap.js* " und " *Bootstrap. CSS* " konnten aus dem ASP.NET-MVC-Projekt in das Verzeichnis " *wwwroot* " im neuen Projekt kopiert werden. Stattdessen bietet dieses Dokument Unterstützung für Bootstrap (und andere Client seitige Bibliotheken) mithilfe von CDNs im nächsten Abschnitt.

## <a name="migrate-the-layout-file"></a>Migrieren der Layoutdatei

* Kopieren Sie die Datei *_ViewStart. cshtml* aus dem Verzeichnis *views* des ASP.NET MVC-Projekts in das Verzeichnis *views* des ASP.net Core Projekts. Die Datei *_ViewStart. cshtml* wurde in ASP.net Core MVC nicht geändert.

* Erstellen Sie ein *views/Shared-* Verzeichnis.

* *Optional:* Kopieren Sie *_ViewImports. cshtml* aus dem Verzeichnis *views* -Verzeichnis des Projekts " *fullaspnetcore* " in das Verzeichnis " *views* " des ASP.net Core Projekts. Entfernen Sie eine beliebige Namespace Deklaration in der Datei *_ViewImports. cshtml* . Die Datei *_ViewImports. cshtml* enthält Namespaces für alle Ansichts Dateien und führt [taghilfsprogramme](xref:mvc/views/tag-helpers/intro)aus. Taghilfsprogramme werden in der neuen Layoutdatei verwendet. Die Datei *_ViewImports. cshtml* ist neu für ASP.net Core.

* Kopieren Sie die Datei *_Layout. cshtml* aus dem Verzeichnis *views/Shared* des ASP.NET-MVC-Projekts in die *Ansichten/* das freigegebene Verzeichnis des ASP.net Core Projekts.

Öffnen Sie *_Layout. cshtml* -Datei, und nehmen Sie die folgenden Änderungen vor (der abgeschlossene Code wird unten angezeigt):

* Ersetzen `@Styles.Render("~/Content/css")` Sie dies durch ein- `<link>` Element, das *Bootstrap. CSS* laden soll (siehe unten).

* Entfernen Sie `@Scripts.Render("~/bundles/modernizr")`.

* Kommentieren Sie die `@Html.Partial("_LoginPartial")` Zeile aus (umschließen Sie die Zeile mit `@*...*@` ). Weitere Informationen finden Sie unter [Migrieren der Authentifizierung und Identity zu ASP.net Core](xref:migration/identity)

* Ersetzen Sie dies `@Scripts.Render("~/bundles/jquery")` durch ein- `<script>` Element (siehe unten).

* Ersetzen Sie dies `@Scripts.Render("~/bundles/bootstrap")` durch ein- `<script>` Element (siehe unten).

Das Ersetzungs Markup für die Bootstrap-CSS-Einbindung:

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

Das Ersetzungs Markup für die jQuery-und Bootstrap-JavaScript-Einbindung:

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

Die aktualisierte Datei *_Layout. cshtml* ist unten dargestellt:

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

Zeigen Sie die Website im Browser an. Es sollte jetzt ordnungsgemäß geladen werden, wobei die erwarteten Stile vorhanden sind.

* *Optional:* Versuchen Sie, die neue Layoutdatei zu verwenden. Kopieren Sie die Layoutdatei aus dem *fullaspnetcore* -Projekt. Die neue Layoutdatei verwendet [taghilfsprogramme](xref:mvc/views/tag-helpers/intro) und bietet weitere Verbesserungen.

## <a name="configure-bundling-and-minification"></a>Konfigurieren der Bündelung und Minimierung

Weitere Informationen zum Konfigurieren von Bündelung und Minimierung finden Sie unter [Bündelung und Minimierung](xref:client-side/bundling-and-minification).

## <a name="solve-http-500-errors"></a>Beheben von HTTP 500-Fehlern

Es gibt viele Probleme, die eine HTTP 500-Fehlermeldung verursachen können, die keine Informationen zur Ursache des Problems enthält. Wenn die Datei *views/_ViewImports. cshtml* beispielsweise einen Namespace enthält, der im Projekt nicht vorhanden ist, wird ein HTTP 500-Fehler generiert. Standardmäßig wird die Erweiterung in ASP.net Core `UseDeveloperExceptionPage` -apps dem hinzugefügt `IApplicationBuilder` und ausgeführt, wenn die Konfiguration *entwickelt*wird. Sehen Sie sich ein Beispiel im folgenden Code an:

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

ASP.net Core konvertiert nicht behandelte Ausnahmen in HTTP 500-Fehler Antworten. Normalerweise werden Fehlerdetails nicht in diese Antworten eingeschlossen, um das Offenlegen von potenziell sensiblen Informationen über den Server zu verhindern. Weitere Informationen finden Sie auf der [Seite Entwickler Ausnahme](xref:fundamentals/error-handling#developer-exception-page).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

In diesem Artikel wird gezeigt, wie Sie mit der Migration eines ASP.NET MVC-Projekts zu [ASP.net Core MVC](xref:mvc/overview) 2,1 beginnen. In diesem Prozess werden viele der Dinge hervorgehoben, die sich seit ASP.NET MVC geändert haben. Die Migration von ASP.NET MVC ist ein mehrstufiger Prozess. In diesem Artikel wird Folgendes behandelt:

* Erste Einrichtung
* Grundlegende Controller und Ansichten
* Statischer Inhalt
* Client seitige Abhängigkeiten.

Informationen zum Migrieren von Konfiguration und Identity Code finden [Sie unter Migrieren der Konfiguration zu ASP.net Core](xref:migration/configuration) und [Migrieren der Authentifizierung und Identity ASP.net Core](xref:migration/identity).

> [!NOTE]
> Die Versionsnummern in den Beispielen sind möglicherweise nicht aktuell, aktualisieren Sie die Projekte entsprechend.

## <a name="create-the-starter-aspnet-mvc-project"></a>Erstellen des MVC-Projekts Starter ASP.net

Um das Upgrade zu veranschaulichen, erstellen wir zunächst eine ASP.NET MVC-app. Erstellen Sie die Datei mit dem Namen *"WebApp1"* , damit der Namespace mit dem im nächsten Schritt erstellten ASP.net Core Projekt übereinstimmt.

![Visual Studio - Dialogfeld „Neues Projekt“](mvc/_static/new-project.png)

![Dialogfeld "neue Webanwendung": MVC-Projektvorlage im Bereich "ASP.net Templates"](mvc/_static/new-project-select-mvc-template.png)

*Optional:* Ändern Sie den Namen der Projekt Mappe von *"WebApp1"* in *Mvc5*. Visual Studio zeigt den neuen Projektmappennamen an (*Mvc5*). Dadurch wird das Projekt leichter vom nächsten Projekt aus informiert.

## <a name="create-the-aspnet-core-project"></a>Erstellen des ASP.net Core Projekts

Erstellen Sie eine neue *leere* ASP.net Core Web-App mit dem gleichen Namen wie das vorherige Projekt (*"WebApp1"*), damit die Namespaces in den beiden Projekten übereinstimmen. Wenn Sie den gleichen Namespace haben, ist es einfacher, Code zwischen den beiden Projekten zu kopieren. Erstellen Sie dieses Projekt in einem anderen Verzeichnis als das vorherige Projekt, um denselben Namen zu verwenden.

![Dialogfeld "Neues Projekt"](mvc/_static/new_core.png)

![Dialogfeld "neue ASP.NET Webanwendung": leere Projektvorlage im Bereich "ASP.net Core Vorlagen" ausgewählt](mvc/_static/new-project-select-empty-aspnet5-template.png)

* *Optional:* Erstellen Sie mithilfe der Projektvorlage für die *Webanwendung* eine neue ASP.net Core-app. Nennen Sie das Projekt *"WebApp1"*, und wählen Sie eine Authentifizierungs Option **einzelner Benutzerkonten**aus. Benennen Sie diese APP in *fullaspnetcore*um. Wenn Sie dieses Projekt erstellen, sparen Sie Zeit in der Konvertierung. Das Endergebnis kann im von der Vorlage generierten Code angezeigt werden, Code kann in das Konvertierungs Projekt kopiert oder mit dem Vorlagen generierten Projekt verglichen werden.

## <a name="configure-the-site-to-use-mvc"></a>Konfigurieren des Standorts für die Verwendung von MVC

* Wenn .net Core als Ziel verwendet wird, wird standardmäßig auf das [Metapaket Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app) verwiesen. Dieses Paket enthält Pakete, die häufig von MVC-Apps verwendet werden. Wenn Sie .NET Framework adressieren, müssen Paket Verweise einzeln in der Projektdatei aufgelistet werden.

`Microsoft.AspNetCore.Mvc` ist das ASP.net Core MVC-Framework. `Microsoft.AspNetCore.StaticFiles` ist der statische Datei Handler. ASP.net Core-apps entscheiden sich explizit für Middleware, z. b. für die Betreuung statischer Dateien. Weitere Informationen finden Sie im Artikel zu [statischen Dateien](xref:fundamentals/static-files).

* Öffnen Sie die Datei *Startup.cs* , und ändern Sie den Code so, dass er dem folgenden entspricht:

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

Die <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> Erweiterungsmethode fügt den statischen Datei Handler hinzu. Die `UseMvc` Erweiterungsmethode fügt Routing hinzu. Weitere Informationen finden Sie unter [starten](xref:fundamentals/startup) und [Routing](xref:fundamentals/routing)von Anwendungen.

## <a name="add-a-controller-and-view"></a>Controller und Ansicht hinzufügen

In diesem Abschnitt werden ein minimaler Controller und eine Ansicht hinzugefügt, die als Platzhalter für den ASP.NET MVC-Controller und im nächsten Abschnitt migrierte Sichten fungieren.

* Fügen Sie ein *Controller* Verzeichnis hinzu.

* Fügen Sie dem Verzeichnis *Controller* eine **Controller Klasse** mit dem Namen *HomeController.cs* hinzu.

![Dialogfeld „Neues Element hinzufügen“](mvc/_static/add_mvc_ctl.png)

* Fügen Sie ein *views* -Verzeichnis hinzu.

* Fügen Sie ein *views/Home-* Verzeichnis hinzu.

* Fügen Sie dem Verzeichnis *views/Home* eine ** Razor Ansicht** mit dem Namen *Index. cshtml* hinzu.

![Dialogfeld „Neues Element hinzufügen“](mvc/_static/view.png)

Die Projektstruktur wird unten dargestellt:

![Projektmappen-Explorer anzeigen von Dateien und Verzeichnissen von "WebApp1"](mvc/_static/project-structure-controller-view.png)

Ersetzen Sie die Inhalte der Datei *Views/HelloWorld/Index.cshtml* durch das folgende Markup:

```html
<h1>Hello world!</h1>
```

Führen Sie die App aus.

![Die Web-App wird in Microsoft Edge geöffnet.](mvc/_static/hello-world.png)

Weitere Informationen finden Sie unter [Controller](xref:mvc/controllers/actions) und [Ansichten](xref:mvc/views/overview).

Die folgende Funktionalität erfordert eine Migration von dem Beispiel ASP.NET MVC-Projekt zum ASP.net Core Projekt:

* Client seitiger Inhalt (CSS, Schriftarten und Skripts)

* Controller

* views

* Modelle

* anbietet

* Filter

* Melden Sie sich an/aus Identity (Dies erfolgt im nächsten Tutorial).

## <a name="controllers-and-views"></a>Controller und Ansichten

* Kopieren Sie jede Methode aus ASP.NET MVC `HomeController` in die neue `HomeController` . In ASP.NET MVC lautet der Rückgabetyp der Controller Aktionsmethode der integrierten Vorlage <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> ; in ASP.net Core MVC geben die Aktionsmethoden `IActionResult` stattdessen zurück. `ActionResult` implementiert `IActionResult` , sodass es nicht erforderlich ist, den Rückgabetyp der Aktionsmethoden zu ändern.

* Kopieren Sie die Ansichts Dateien " *about. cshtml*", " *Contact. cshtml*" und " *Index. cshtml* " Razor vom ASP.NET-MVC-Projekt in das ASP.net Core-Projekt.

## <a name="test-each-method"></a>Jede Methode testen

Die Layoutdatei und-Stile wurden noch nicht migriert, sodass die gerenderten Sichten nur den Inhalt der Ansichts Dateien enthalten. Die von der Layoutdatei generierten `About` `Contact` Verknüpfungen für die Ansichten und sind noch nicht verfügbar.

* Rufen Sie die gerenderten Sichten aus dem Browser auf der laufenden ASP.net Core-App auf, indem Sie die aktuelle Portnummer durch die im ASP.net Core-Projekt verwendete Portnummer ersetzen. Beispiel: `https://localhost:44375/home/about`.

![Kontaktseite](mvc/_static/contact-page.png)

Beachten Sie den Mangel an Formatierungs-und Menü Elementen. Die Formatierung wird im nächsten Abschnitt korrigiert.

## <a name="static-content"></a>Statischer Inhalt

In ASP.NET MVC 5 und früheren Versionen wurde statischer Inhalt aus dem Stammverzeichnis des Webprojekts gehostet und mit serverseitigen Dateien gemischt. In ASP.net Core wird statischer Inhalt im Verzeichnis " *wwwroot* " gehostet. Kopieren Sie den statischen Inhalt aus der ASP.NET MVC-app in das Verzeichnis " *wwwroot* " im ASP.net Core-Projekt. In dieser Beispiel Konvertierung:

* Kopieren Sie die Datei " *Favicon. ico* " aus dem ASP.NET-MVC-Projekt in das Verzeichnis " *wwwroot* " im ASP.net Core-Projekt.

Das ASP.NET MVC-Projekt verwendet [Bootstrap](https://getbootstrap.com/) für seine Formatierung und speichert die Bootstrap-Dateien in den Verzeichnissen " *Content* " und " *Scripts* ". Die Vorlage, die das ASP.NET MVC-Projekt generiert hat, verweist auf Bootstrap in der Layoutdatei (*views/Shared/_Layout. cshtml*). Die Dateien " *bootstrap.js* " und " *Bootstrap. CSS* " konnten aus dem ASP.NET-MVC-Projekt in das Verzeichnis " *wwwroot* " im neuen Projekt kopiert werden. Stattdessen bietet dieses Dokument Unterstützung für Bootstrap (und andere Client seitige Bibliotheken) mithilfe von CDNs im nächsten Abschnitt.

## <a name="migrate-the-layout-file"></a>Migrieren der Layoutdatei

* Kopieren Sie die Datei *_ViewStart. cshtml* aus dem Verzeichnis *views* des ASP.NET MVC-Projekts in das Verzeichnis *views* des ASP.net Core Projekts. Die Datei *_ViewStart. cshtml* wurde in ASP.net Core MVC nicht geändert.

* Erstellen Sie ein *views/Shared-* Verzeichnis.

* *Optional:* Kopieren Sie *_ViewImports. cshtml* aus dem Verzeichnis *views* -Verzeichnis des Projekts " *fullaspnetcore* " in das Verzeichnis " *views* " des ASP.net Core Projekts. Entfernen Sie eine beliebige Namespace Deklaration in der Datei *_ViewImports. cshtml* . Die Datei *_ViewImports. cshtml* enthält Namespaces für alle Ansichts Dateien und führt [taghilfsprogramme](xref:mvc/views/tag-helpers/intro)aus. Taghilfsprogramme werden in der neuen Layoutdatei verwendet. Die Datei *_ViewImports. cshtml* ist neu für ASP.net Core.

* Kopieren Sie die Datei *_Layout. cshtml* aus dem Verzeichnis *views/Shared* des ASP.NET-MVC-Projekts in die *Ansichten/* das freigegebene Verzeichnis des ASP.net Core Projekts.

Öffnen Sie *_Layout. cshtml* -Datei, und nehmen Sie die folgenden Änderungen vor (der abgeschlossene Code wird unten angezeigt):

* Ersetzen `@Styles.Render("~/Content/css")` Sie dies durch ein- `<link>` Element, das *Bootstrap. CSS* laden soll (siehe unten).

* Entfernen Sie `@Scripts.Render("~/bundles/modernizr")`.

* Kommentieren Sie die `@Html.Partial("_LoginPartial")` Zeile aus (umschließen Sie die Zeile mit `@*...*@` ). Weitere Informationen finden Sie unter [Migrieren der Authentifizierung und Identity zu ASP.net Core](xref:migration/identity)

* Ersetzen Sie dies `@Scripts.Render("~/bundles/jquery")` durch ein- `<script>` Element (siehe unten).

* Ersetzen Sie dies `@Scripts.Render("~/bundles/bootstrap")` durch ein- `<script>` Element (siehe unten).

Das Ersetzungs Markup für die Bootstrap-CSS-Einbindung:

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

Das Ersetzungs Markup für die jQuery-und Bootstrap-JavaScript-Einbindung:

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

Die aktualisierte Datei *_Layout. cshtml* ist unten dargestellt:

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

Zeigen Sie die Website im Browser an. Es sollte jetzt ordnungsgemäß geladen werden, wobei die erwarteten Stile vorhanden sind.

* *Optional:* Versuchen Sie, die neue Layoutdatei zu verwenden. Kopieren Sie die Layoutdatei aus dem *fullaspnetcore* -Projekt. Die neue Layoutdatei verwendet [taghilfsprogramme](xref:mvc/views/tag-helpers/intro) und bietet weitere Verbesserungen.

## <a name="configure-bundling-and-minification"></a>Konfigurieren der Bündelung und Minimierung

Weitere Informationen zum Konfigurieren von Bündelung und Minimierung finden Sie unter [Bündelung und Minimierung](xref:client-side/bundling-and-minification).

## <a name="solve-http-500-errors"></a>Beheben von HTTP 500-Fehlern

Es gibt viele Probleme, die eine HTTP 500-Fehlermeldung verursachen können, die keine Informationen zur Ursache des Problems enthält. Wenn die Datei *views/_ViewImports. cshtml* beispielsweise einen Namespace enthält, der im Projekt nicht vorhanden ist, wird ein HTTP 500-Fehler generiert. Standardmäßig wird die Erweiterung in ASP.net Core `UseDeveloperExceptionPage` -apps dem hinzugefügt `IApplicationBuilder` und ausgeführt, wenn die Konfiguration *entwickelt*wird. Sehen Sie sich ein Beispiel im folgenden Code an:

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

ASP.net Core konvertiert nicht behandelte Ausnahmen in HTTP 500-Fehler Antworten. Normalerweise werden Fehlerdetails nicht in diese Antworten eingeschlossen, um das Offenlegen von potenziell sensiblen Informationen über den Server zu verhindern. Weitere Informationen finden Sie auf der [Seite Entwickler Ausnahme](xref:fundamentals/error-handling#developer-exception-page).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end
