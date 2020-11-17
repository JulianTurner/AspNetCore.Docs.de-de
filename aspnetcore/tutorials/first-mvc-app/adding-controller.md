---
title: 'Teil 2: Hinzufügen eines Controllers zu einer ASP.NET Core MVC-App'
author: rick-anderson
description: Dies ist Teil 2 der Tutorialreihe zu ASP.NET Core MVC.
ms.author: riande
ms.date: 08/05/2017
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
uid: tutorials/first-mvc-app/adding-controller
ms.openlocfilehash: 1851a61441ed351442f86939022319ad59d10006
ms.sourcegitcommit: 91e14f1e2a25c98a57c2217fe91b172e0ff2958c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422618"
---
# <a name="part-2-add-a-controller-to-an-aspnet-core-mvc-app"></a>Teil 2: Hinzufügen eines Controllers zu einer ASP.NET Core MVC-App

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Das Architekturmodell Model-View-Controller (MVC) trennt eine App in drei Hauptkomponenten: **M** odel (Modell), **V** iew (Ansicht) und **C** ontroller. Das MVC-Muster hilft beim Erstellen von Apps, die einfacher zu testen und zu aktualisieren sind als herkömmliche monolithische Apps. MVC-basierte Apps enthalten Folgendes:

* **M** odelle: Klassen, die die Daten der App darstellen. Die Modellklassen verwenden Validierungslogik zum Erzwingen von Geschäftsregeln für diese Daten. In der Regel wird der Modellstatus von Modellobjekten in einer Datenbank abgerufen und gespeichert. In diesem Tutorial ruft ein `Movie`-Modell Daten aus einer Datenbank ab, stellt sie der Ansicht bereit und aktualisiert sie. Aktualisierte Daten werden in eine Datenbank geschrieben.

* **V** iews (Ansichten): Ansichten sind die Komponenten, die die Benutzeroberfläche der App anzeigen. Im Allgemeinen werden die Modelldaten auf dieser Benutzeroberfläche angezeigt.

* **C** ontroller: Klassen, die Browseranforderungen verarbeiten. Sie rufen Modelldaten ab und rufen Ansichtsvorlagen auf, die eine Antwort zurückgeben. In einer MVC-Anwendung zeigt die Ansicht nur Informationen. Benutzereingaben und -interaktionen werden vom Controller beantwortet und verarbeitet. Der Controller verarbeitet beispielsweise Routendaten und Werte von Abfragezeichenfolgen, die an das Modell übergeben werden. Das Modell kann diese Werte nutzen, um die Datenbank abzufragen. Beispiel: `https://localhost:5001/Home/Privacy` hat Routendaten von `Home` (Controller) und `Privacy` (für den Controller „Home“ aufzurufende Aktionsmethode). `https://localhost:5001/Movies/Edit/5` ist eine Anforderung zum Bearbeiten des Films mit der ID 5 mithilfe des „movie“-Controllers. Routendaten werden weiter unten im Tutorial erläutert.

Das MVC-Muster hilft Ihnen beim Erstellen von Apps, die deren verschiedenen Aspekte (Eingabelogik, Geschäftslogik und Benutzeroberflächenlogik) trennt und zugleich eine lose Kopplung zwischen diesen Elementen bietet. Das Muster gibt an, wo sich jede Art von Logik in der App befinden soll. Die Benutzeroberflächenlogik ist Teil der Ansicht (view). Die Eingabelogik gehört zum Controller. Die Geschäftslogik gehört zum Modell. Diese Trennung ermöglicht Ihnen das Bewältigen von Komplexität beim Erstellen einer App, da Sie zu einem Zeitpunkt an einem Aspekt der Implementierung arbeiten können, ohne den Code eines anderen zu beeinträchtigen. Sie können beispielsweise am Code der Ansicht unabhängig vom Code für die Geschäftslogik arbeiten.

Wir behandeln diese Konzepte in dieser Tutorialreihe und zeigen Ihnen, wie Sie sie zum Erstellen einer Film-App nutzen. Das MVC-Projekt enthält Ordner für die *Controller* und *Views* (Ansichten).

## <a name="add-a-controller"></a>Hinzufügen eines Controllers

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Controller > Hinzufügen > Controller**
  ![](~/tutorials/first-mvc-app/adding-controller/_static/add_controllerCopy.png).

* Wählen Sie im Dialogfeld **Gerüst hinzufügen** die Option **MVC-Controller - leer** aus.

  ![Hinzufügen und Benennen des MVC-Controllers](~/tutorials/first-mvc-app/adding-controller/_static/acCopy.png)

* Geben Sie im **Dialogfeld zum Hinzufügen eines leeren MVC-Controllers**  den Namen **HelloWorldController** ein, und wählen Sie **Hinzufügen** aus.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Wählen Sie das **EXPLORER**-Symbol aus, klicken Sie dann bei gedrückter STRG-TASTE bzw. mit der rechten Maustaste auf **Controller > Neue Datei**, und geben Sie der neuen Datei den Namen *HelloWorldController.cs*.

  ![Kontextmenü](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_file.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Controller > Hinzufügen > Neue Datei**.
![Kontextmenü](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)

Wählen Sie **ASP.NET Core** und **-Controllerklasse** aus.

Nennen Sie den Controller **HelloWorldController**.

![Hinzufügen und Benennen des MVC-Controllers](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

Ersetzen Sie den Inhalt von *Controllers/HelloWorldController.cs* durch Folgendes:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

Jede `public`-Methode in einem Controller kann als HTTP-Endpunkt aufgerufen werden. Im obigen Beispiel geben beide Methoden eine Zeichenfolge zurück. Beachten Sie die Kommentare vor jeder Methode.

Ein HTTP-Endpunkt ist eine Ziel-URL in der Webanwendung, wie z.B. `https://localhost:5001/HelloWorld`, und kombiniert das verwendete Protokoll `HTTPS`, die Netzwerkadresse des Webservers (einschließlich TCP-Port) `localhost:5001` und den Ziel-URI `HelloWorld`.

Der erste Kommentar besagt, dass es sich dabei um eine [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp)-Methode handelt, die durch Anfügen von `/HelloWorld/` an die Basis-URL aufgerufen wird. Der zweite Kommentar gibt eine [HTTP GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)-Methode an, die aufgerufen wird, indem `/HelloWorld/Welcome/` an die URL angefügt wird. Im weiteren Verlauf des Tutorials wird die Gerüstbau-Engine verwendet, um `HTTP POST`-Methoden zu erstellen, mit denen Daten aktualisiert werden.

Führen Sie die App im Nicht-Debugmodus aus, und fügen Sie „HelloWorld“ an den Pfad in der Adressleiste an. Die `Index`-Methode gibt eine Zeichenfolge zurück.

![Browserfenster mit der Anwendungsantwort „This is my default action“](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

MVC ruft Controllerklassen (und darin enthaltene Aktionsmethoden) abhängig von der eingehenden URL auf. Die von MVC verwendete standardmäßige [URL-Routinglogik](xref:mvc/controllers/routing) befolgt ein Format wie dieses, um den aufzurufenden Code zu bestimmen:

`/[Controller]/[ActionName]/[Parameters]`

Das Routingformat ist in der `Configure`-Methode in der Datei *Startup.cs* festgelegt.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

Wenn Sie zu der App navigieren und keine URL-Segmente angeben, werden standardmäßig der Controller „Home“ und die Methode „Index“ verwendet, die in der oben hervorgehobenen Vorlagenzeile angegeben ist.

Das erste URL-Segment bestimmt die auszuführende Controllerklasse. Daher wird `localhost:{PORT}/HelloWorld` der **HelloWorld**-Controllerklasse zugeordnet. Der zweite Teil des URL-Segments bestimmt die Aktionsmethode für die Klasse. Daher bewirkt `localhost:{PORT}/HelloWorld/Index` das Ausführen der `Index`-Methode der `HelloWorldController`-Klasse. Beachten Sie, dass Sie nur zu `localhost:{PORT}/HelloWorld` navigieren mussten und die `Index`-Methode standardmäßig aufgerufen wurde. Der Grund hierfür ist, dass `Index` die Standardmethode ist, die für einen Controller aufgerufen wird, wenn der Methodenname nicht explizit angegeben wird. Der dritte Teil des URL-Segments (`id`) ist für Routendaten. Routendaten werden weiter unten im Tutorial erläutert.

Wechseln Sie zu `https://localhost:{PORT}/HelloWorld/Welcome`. Die `Welcome`-Methode wird ausgeführt und gibt die Zeichenfolge `This is the Welcome action method...` zurück. Bei dieser URL ist `HelloWorld` der Controller und `Welcome` die Aktionsmethode. Sie haben den Teil `[Parameters]` der URL noch nicht verwendet.

![Browserfenster mit der Anwendungsantwort „This is the Welcome action method“](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

Ändern Sie den Code so, dass Parameterinformationen von der URL an den Controller übergeben werden. Beispielsweise `/HelloWorld/Welcome?name=Rick&numtimes=4`. Ändern Sie `Welcome`-Methode so, dass zwei Parameter, wie im folgenden Code gezeigt, einbezogen werden.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

Der vorangehende Code:

* Verwendet das C#-Feature „optional-parameter“, um anzugeben, dass der `numTimes`-Parameter standardmäßig 1 ist, wenn kein anderer Wert für diesen Parameter übergeben wird. <!-- remove for simplified -->
* Verwendet `HtmlEncoder.Default.Encode`, um die App vor schädlicher Eingaben (über JavaScript) zu schützen.
* Verwendet [interpolierte Zeichenfolgen](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) in `$"Hello {name}, NumTimes is: {numTimes}"`. <!-- remove for simplified -->

Führen Sie die App aus, und navigieren Sie zu:

   `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

(Ersetzen Sie `{PORT}` durch Ihre Portnummer.) Sie können für `name` und `numtimes` in der URL verschiedene Werte ausprobieren. Das MVC-[Modellbindungssystem](xref:mvc/models/model-binding) ordnet automatisch die benannten Parameter aus der Abfragezeichenfolge auf der Adressleiste den Parametern der Methode zu. Weitere Informationen finden Sie unter [Modellbindung](xref:mvc/models/model-binding).

![Browserfenster mit der Anwendungsantwort Hello Rick, NumTimes is\: 4](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

In der obigen Abbildung wird das URL-Segment (`Parameters`) nicht verwendet, und die Parameter `name` und `numTimes` werden in der [Abfragezeichenfolge](https://wikipedia.org/wiki/Query_string) übergeben. Das Fragezeichen (`?`) in der obigen URL ist ein Trennzeichen, danach folgt die Abfragezeichenfolge. Das Zeichen `&` trennt Feld-Wert-Paare.

Ersetzen Sie die `Welcome`-Methode durch folgenden Code:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

Führen Sie die App aus, und geben Sie die folgende URL ein: `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`

Dieses Mal hat das dritte URL-Segment mit dem Routenparameter `id` übereingestimmt. Die `Welcome`-Methode enthält den Parameter `id`, der mit der URL-Vorlage in der `MapControllerRoute`-Methode übereinstimmt. Das nachfolgende `?` (in `id?`) gibt an, dass der Parameter `id` optional ist.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

In diesen Beispielen hat der Controller den „VC“-Teil von MVC übernommen, d. h. die Aufgaben von **V** iew und **C** ontroller. Der Controller gibt direkt HTML zurück. Im Allgemeinen sollen Controller nicht direkt HTML zurückgeben, da dies sehr aufwändig zu programmieren und pflegen ist. Stattdessen verwenden Sie in der Regel eine separate Razor-Ansichtsvorlagendatei, um die HTML-Antwort zu generieren. Dies lernen Sie im nächsten Tutorial.

> [!div class="step-by-step"]
> [Zurück](start-mvc.md)
> [Weiter](adding-view.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Das Architekturmodell Model-View-Controller (MVC) trennt eine App in drei Hauptkomponenten: **M** odel (Modell), **V** iew (Ansicht) und **C** ontroller. Das MVC-Muster hilft beim Erstellen von Apps, die einfacher zu testen und zu aktualisieren sind als herkömmliche monolithische Apps. MVC-basierte Apps enthalten Folgendes:

* **M** odelle: Klassen, die die Daten der App darstellen. Die Modellklassen verwenden Validierungslogik zum Erzwingen von Geschäftsregeln für diese Daten. In der Regel wird der Modellstatus von Modellobjekten in einer Datenbank abgerufen und gespeichert. In diesem Tutorial ruft ein `Movie`-Modell Daten aus einer Datenbank ab, stellt sie der Ansicht bereit und aktualisiert sie. Aktualisierte Daten werden in eine Datenbank geschrieben.

* **V** iews (Ansichten): Ansichten sind die Komponenten, die die Benutzeroberfläche der App anzeigen. Im Allgemeinen werden die Modelldaten auf dieser Benutzeroberfläche angezeigt.

* **C** ontroller: Klassen, die Browseranforderungen verarbeiten. Sie rufen Modelldaten ab und rufen Ansichtsvorlagen auf, die eine Antwort zurückgeben. In einer MVC-Anwendung zeigt die Ansicht nur Informationen. Benutzereingaben und -interaktionen werden vom Controller beantwortet und verarbeitet. Der Controller verarbeitet beispielsweise Routendaten und Werte von Abfragezeichenfolgen, die an das Modell übergeben werden. Das Modell kann diese Werte nutzen, um die Datenbank abzufragen. Beispiel: `https://localhost:5001/Home/About` hat Routendaten von `Home` (Controller) und `About` (für den Controller „Home“ aufzurufende Aktionsmethode). `https://localhost:5001/Movies/Edit/5` ist eine Anforderung zum Bearbeiten des Films mit der ID 5 mithilfe des „movie“-Controllers. Routendaten werden weiter unten im Tutorial erläutert.

Das MVC-Muster hilft Ihnen beim Erstellen von Apps, die deren verschiedenen Aspekte (Eingabelogik, Geschäftslogik und Benutzeroberflächenlogik) trennt und zugleich eine lose Kopplung zwischen diesen Elementen bietet. Das Muster gibt an, wo sich jede Art von Logik in der App befinden soll. Die Benutzeroberflächenlogik ist Teil der Ansicht (view). Die Eingabelogik gehört zum Controller. Die Geschäftslogik gehört zum Modell. Diese Trennung ermöglicht Ihnen das Bewältigen von Komplexität beim Erstellen einer App, da Sie zu einem Zeitpunkt an einem Aspekt der Implementierung arbeiten können, ohne den Code eines anderen zu beeinträchtigen. Sie können beispielsweise am Code der Ansicht unabhängig vom Code für die Geschäftslogik arbeiten.

Wir behandeln diese Konzepte in dieser Tutorialreihe und zeigen Ihnen, wie Sie sie zum Erstellen einer Film-App nutzen. Das MVC-Projekt enthält Ordner für die *Controller* und *Views* (Ansichten).

## <a name="add-a-controller"></a>Hinzufügen eines Controllers

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Controller > Hinzufügen > Controller**.
  ![Kontextmenü](~/tutorials/first-mvc-app/adding-controller/_static/add_controller.png)

* Wählen Sie im Dialogfeld **Gerüst hinzufügen** die Option **MVC-Controller - leer** aus.

  ![Hinzufügen und Benennen des MVC-Controllers](~/tutorials/first-mvc-app/adding-controller/_static/ac.png)

* Geben Sie im **Dialogfeld zum Hinzufügen eines leeren MVC-Controllers**  den Namen **HelloWorldController** ein, und wählen Sie **Hinzufügen** aus.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Wählen Sie das **EXPLORER**-Symbol aus, klicken Sie dann bei gedrückter STRG-TASTE bzw. mit der rechten Maustaste auf **Controller > Neue Datei**, und geben Sie der neuen Datei den Namen *HelloWorldController.cs*.

  ![Kontextmenü](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_file.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Controller > Hinzufügen > Neue Datei**.
![Kontextmenü](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)

Wählen Sie **ASP.NET Core** und **MVC-Controller-Klasse** aus.

Nennen Sie den Controller **HelloWorldController**.

![Hinzufügen und Benennen des MVC-Controllers](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

Ersetzen Sie den Inhalt von *Controllers/HelloWorldController.cs* durch Folgendes:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

Jede `public`-Methode in einem Controller kann als HTTP-Endpunkt aufgerufen werden. Im obigen Beispiel geben beide Methoden eine Zeichenfolge zurück. Beachten Sie die Kommentare vor jeder Methode.

Ein HTTP-Endpunkt ist eine Ziel-URL in der Webanwendung, wie z.B. `https://localhost:5001/HelloWorld`, und kombiniert das verwendete Protokoll `HTTPS`, die Netzwerkadresse des Webservers (einschließlich TCP-Port) `localhost:5001` und den Ziel-URI `HelloWorld`.

Der erste Kommentar besagt, dass es sich dabei um eine [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp)-Methode handelt, die durch Anfügen von `/HelloWorld/` an die Basis-URL aufgerufen wird. Der zweite Kommentar gibt eine [HTTP GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)-Methode an, die aufgerufen wird, indem `/HelloWorld/Welcome/` an die URL angefügt wird. Im weiteren Verlauf des Tutorials wird die Gerüstbau-Engine verwendet, um `HTTP POST`-Methoden zu erstellen, mit denen Daten aktualisiert werden.

Führen Sie die App im Nicht-Debugmodus aus, und fügen Sie „HelloWorld“ an den Pfad in der Adressleiste an. Die `Index`-Methode gibt eine Zeichenfolge zurück.

![Browserfenster mit der Anwendungsantwort „This is my default action“](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

MVC ruft Controllerklassen (und darin enthaltene Aktionsmethoden) abhängig von der eingehenden URL auf. Die von MVC verwendete standardmäßige [URL-Routinglogik](xref:mvc/controllers/routing) befolgt ein Format wie dieses, um den aufzurufenden Code zu bestimmen:

`/[Controller]/[ActionName]/[Parameters]`

Das Routingformat ist in der `Configure`-Methode in der Datei *Startup.cs* festgelegt.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

<!-- 
Add link to explain lambda.
Remove link for simplified tutorial.
-->

Wenn Sie zu der App navigieren und keine URL-Segmente angeben, werden standardmäßig der Controller „Home“ und die Methode „Index“ verwendet, die in der oben hervorgehobenen Vorlagenzeile angegeben ist.

Das erste URL-Segment bestimmt die auszuführende Controllerklasse. Daher wird `localhost:{PORT}/HelloWorld` der `HelloWorldController`-Klasse zugeordnet. Der zweite Teil des URL-Segments bestimmt die Aktionsmethode für die Klasse. Daher bewirkt `localhost:{PORT}/HelloWorld/Index` das Ausführen der `Index`-Methode der `HelloWorldController`-Klasse. Beachten Sie, dass Sie nur zu `localhost:{PORT}/HelloWorld` navigieren mussten und die `Index`-Methode standardmäßig aufgerufen wurde. Der Grund hierfür ist, dass `Index` die Standardmethode ist, die für einen Controller aufgerufen wird, wenn der Methodenname nicht explizit angegeben wird. Der dritte Teil des URL-Segments (`id`) ist für Routendaten. Routendaten werden weiter unten im Tutorial erläutert.

Wechseln Sie zu `https://localhost:{PORT}/HelloWorld/Welcome`. Die `Welcome`-Methode wird ausgeführt und gibt die Zeichenfolge `This is the Welcome action method...` zurück. Bei dieser URL ist `HelloWorld` der Controller und `Welcome` die Aktionsmethode. Sie haben den Teil `[Parameters]` der URL noch nicht verwendet.

![Browserfenster mit der Anwendungsantwort „This is the Welcome action method“](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

Ändern Sie den Code so, dass Parameterinformationen von der URL an den Controller übergeben werden. Beispielsweise `/HelloWorld/Welcome?name=Rick&numtimes=4`. Ändern Sie `Welcome`-Methode so, dass zwei Parameter, wie im folgenden Code gezeigt, einbezogen werden.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

Der vorangehende Code:

* Verwendet das C#-Feature „optional-parameter“, um anzugeben, dass der `numTimes`-Parameter standardmäßig 1 ist, wenn kein anderer Wert für diesen Parameter übergeben wird. <!-- remove for simplified -->
* Verwendet `HtmlEncoder.Default.Encode`, um die App vor schädlicher Eingaben (über JavaScript) zu schützen.
* Verwendet [interpolierte Zeichenfolgen](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) in `$"Hello {name}, NumTimes is: {numTimes}"`. <!-- remove for simplified -->

Führen Sie die App aus, und navigieren Sie zu:

   `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

(Ersetzen Sie `{PORT}` durch Ihre Portnummer.) Sie können für `name` und `numtimes` in der URL verschiedene Werte ausprobieren. Das MVC-[Modellbindungssystem](xref:mvc/models/model-binding) ordnet automatisch die benannten Parameter aus der Abfragezeichenfolge auf der Adressleiste den Parametern der Methode zu. Weitere Informationen finden Sie unter [Modellbindung](xref:mvc/models/model-binding).

![Browserfenster mit der Anwendungsantwort Hello Rick, NumTimes is\: 4](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

In der obigen Abbildung wird das URL-Segment (`Parameters`) nicht verwendet, und die Parameter `name` und `numTimes` werden in der [Abfragezeichenfolge](https://wikipedia.org/wiki/Query_string) übergeben. Das Fragezeichen (`?`) in der obigen URL ist ein Trennzeichen, danach folgt die Abfragezeichenfolge. Das Zeichen `&` trennt Feld-Wert-Paare.

Ersetzen Sie die `Welcome`-Methode durch folgenden Code:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

Führen Sie die App aus, und geben Sie die folgende URL ein: `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`

Dieses Mal hat das dritte URL-Segment mit dem Routenparameter `id` übereingestimmt. Die `Welcome`-Methode enthält den Parameter `id`, der mit der URL-Vorlage in der `MapRoute`-Methode übereinstimmt. Das nachfolgende `?` (in `id?`) gibt an, dass der Parameter `id` optional ist.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

In diesen Beispielen hat der Controller den „VC“-Teil von MVC übernommen, d. h. die Aufgaben von „View“ (Ansicht) und „Controller“. Der Controller gibt direkt HTML zurück. Im Allgemeinen sollen Controller nicht direkt HTML zurückgeben, da dies sehr aufwändig zu programmieren und pflegen ist. Stattdessen verwenden Sie in der Regel eine separate Razor-Ansichtsvorlagendatei, um die HTML-Antwort zu generieren. Dies lernen Sie im nächsten Tutorial.

> [!div class="step-by-step"]
> [Zurück](start-mvc.md)
> [Weiter](adding-view.md)

::: moniker-end
