---
title: Erste Schritte mit ASP.NET Core MVC
author: rick-anderson
description: Hier finden Sie Informationen zum Einstieg in ASP.NET Core MVC.
ms.author: riande
ms.date: 01/20/2021
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
uid: tutorials/first-mvc-app/start-mvc
ms.custom: contperf-fy21q3
ms.openlocfilehash: aaf930eee351ed757be60f648bce88b182d52799
ms.sourcegitcommit: da5a5bed5718a9f8db59356ef8890b4b60ced6e9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98710772"
---
# <a name="get-started-with-aspnet-core-mvc"></a>Erste Schritte mit ASP.NET Core MVC

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

Dies ist das erste Tutorial einer Tutorialreihe, die Informationen zur ASP.NET Core MVC-Webentwicklung mit Controllern und Ansichten vermittelt.

Nach Abschluss der Reihe verfügen Sie über eine App, mit der Filmdaten verwaltet und angezeigt werden können. Sie lernen Folgendes:

> [!div class="checklist"]
> * Erstellen einer Web-App
> * Hinzufügen eines Modells und Erstellen eines Gerüsts für das Modell
> * Arbeiten mit einer Datenbank
> * Hinzufügen von Such- und Überprüfungsfunktionen

[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).

## <a name="prerequisites"></a>Voraussetzungen

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-app"></a>Erstellen einer Web-App

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Starten Sie Visual Studio, und wählen Sie **Neues Projekt erstellen** aus.
* Wählen Sie im Dialogfeld **Neues Projekt** die Option **ASP.NET Core-Webanwendung** > **Weiter** aus.
* Geben Sie `MvcMovie` im Dialogfeld **Neues Projekt konfigurieren** für **Projektname** ein. Es ist wichtig, *MvcMovie* als Projektnamen anzugeben. Die Groß- und Kleinschreibung muss beim Kopieren von Code bei jedem `namespace` übereinstimmen.
* Klicken Sie auf **Erstellen**.
* Wählen Sie im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** Folgendes aus:
  * In den Dropdownmenüs **.NET Core** und **ASP.NET Core 5.0**.
  * **ASP.NET Core-Web-App (Model View Controller)** .
  * **Erstellen**:

![Erstellen einer neuen ASP.NET Core-Webanwendung ](start-mvc/_static/mvcVS19v16.9.png)

Alternative Ansätze zum Erstellen des Projekts finden Sie unter [Erstellen eines neuen Projekts in Visual Studio](/visualstudio/ide/create-new-project).

Visual Studio hat die Standardprojektvorlage für das erstellte MVC-Projekt verwendet. Für das erstellte Projekt gilt:

* Es handelt sich um eine funktionierende App.
* Es handelt sich um ein einfaches Startprojekt.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Das Tutorial setzt voraus, dass Sie mit Visual Studio Code vertraut sind. Weitere Informationen finden Sie unter [Erste Schritte mit VS Code](https://code.visualstudio.com/docs) und [Hilfe zu Visual Studio Code](#visual-studio-code-help).

* Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Wechseln Sie zu dem Verzeichnis (`cd`), in dem Projekt enthalten sein soll.
* Führen Sie den folgenden Befehl aus:

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * Möglicherweise wird ein Dialogfeld mit folgender Meldung angezeigt: **Die erforderlichen Ressourcen zum Erstellen und Debuggen sind in „MvcMovie“ nicht vorhanden. Hinzufügen?** . Klicken Sie in diesem Fall auf **Ja**.

  * `dotnet new mvc -o MvcMovie`: Erstellt ein neues ASP.NET Core MVC-Projekt im Ordner *MvcMovie*.
  * `code -r MvcMovie`: Lädt die Projektdatei *MvcMovie.csproj* in Visual Studio Code.

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

* Klicken Sie auf **Datei** > **Neue Projektmappe**.

  ![Neue Projektmappe in macOS](start-mvc/_static/new_project_vsmac.png)

* Navigieren Sie in Visual Studio für Mac-Versionen vor Version 8.6 zu **.NET Core** > **App** > **Webanwendung (Model-View-Controller)**  > **Weiter**. Bei Version 8.6 oder höher klicken Sie auf **Web and Console** > **App** > **Webanwendung (Model-View-Controller)**  > **Weiter** (Web und Konsole).

  ![Auswählen der macOS-Web-App-Vorlage](start-mvc/_static/web_app_template_vsmac.png)

* Gehen Sie im Dialogfeld **Neue Webanwendung konfigurieren** folgendermaßen vor:

  * Vergewissern Sie sich, dass **Authentifizierung** auf **Keine Authentifizierung** festgelegt ist.
  * Wenn eine Option zum Auswählen eines **Zielframeworks** angezeigt wird, wählen Sie die neueste 5.x-Version aus.
  * Klicken Sie auf **Weiter**.

* Nennen Sie das Projekt **MvcMovie**, und wählen Sie dann **Erstellen** aus.

  ![Benennen des macOS-Projekts](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a>Ausführen der App

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Drücken Sie STRG+F5, um die App ohne den Debugger auszuführen.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio:

  * Startet [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview).
  * Führt die App aus.

  Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`. Der Standardhostname für Ihren lokalen Computer lautet `localhost`. Wenn Visual Studio ein Webprojekt erstellt, wird ein zufällig ausgewählter Port für den Webserver verwendet.

Das Starten der App ohne Debuggen über STRG+F5 ermöglicht Ihnen Folgendes:

* Nehmen Sie Änderungen am Code vor.
* Speichern Sie die Datei.
* Aktualisieren Sie den Browser, und sehen Sie sich die Codeänderungen an.

Sie können die App über das Menüelement **Debuggen** im Debugmodus oder Nicht-Debugmodus starten:

![Menü „Debuggen“](start-mvc/_static/debug_menu50.png)

Sie können die App debuggen, indem Sie die Schaltfläche **IIS Express** auswählen.

![IIS Express](start-mvc/_static/iis_express50.png)

Auf der folgenden Abbildung sehen Sie die App:

![Start- oder Indexseite](start-mvc/_static/home50-vs.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Drücken Sie STRG+F5, um die App ohne den Debugger auszuführen.

  [!INCLUDE[](~/includes/trustCertVSC.md)]

  Visual Studio Code:

  * Startet [Kestrel](xref:fundamentals/servers/kestrel).
  * Startet einen Browser.
  * Navigiert zu `https://localhost:5001`.

  Die Adressleiste zeigt `localhost:port:5001` an, nicht `example.com`. Der Standardhostname für Ihren lokalen Computer lautet `localhost`. „Localhost“ dient nur Webanforderungen vom lokalen Computer.

Das Starten der App ohne Debuggen über STRG+F5 ermöglicht Ihnen Folgendes:

* Nehmen Sie Änderungen am Code vor.
* Speichern Sie die Datei.
* Aktualisieren Sie den Browser, und sehen Sie sich die Codeänderungen an.

  ![Start- oder Indexseite](start-mvc/_static/home50-port5001.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

* Wählen Sie **Ausführen** > **Ohne Debuggen starten** auf, um die App zu starten.

  Visual Studio für Mac:

  * Startet den [Kestrel](xref:fundamentals/servers/index#kestrel)-Server.
  * Startet einen Browser.
  * Navigiert zu `http://localhost:port`, wobei es sich bei *port* um eine zufällig ausgewählte Portnummer handelt.

  [!INCLUDE[](~/includes/trustCertMac.md)]

  Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`. Der Standardhostname für Ihren lokalen Computer lautet `localhost`. Wenn Visual Studio ein Webprojekt erstellt, wird ein zufällig ausgewählter Port für den Webserver verwendet.

Sie können die App über das Menü **Ausführen** im Debugmodus oder Nicht-Debugmodus starten.

Auf der folgenden Abbildung sehen Sie die App:

![Start- oder Indexseite](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

Im nächsten Teil dieses Tutorials erfahren Sie mehr über MVC und beginnen mit dem Schreiben von Code.

> [!div class="step-by-step"]
> [Weiter: Hinzufügen eines Controllers](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

Dies ist das erste Tutorial einer Tutorialreihe, die Informationen zur ASP.NET Core MVC-Webentwicklung mit Controllern und Ansichten vermittelt.

Nach Abschluss der Reihe verfügen Sie über eine App, mit der Filmdaten verwaltet und angezeigt werden können. Sie lernen Folgendes:

> [!div class="checklist"]
> * Erstellen einer Web-App
> * Hinzufügen eines Modells und Erstellen eines Gerüsts für das Modell
> * Arbeiten mit einer Datenbank
> * Hinzufügen von Such- und Überprüfungsfunktionen

[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).

## <a name="prerequisites"></a>Voraussetzungen

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a>Erstellen einer Web-App

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Wählen Sie in Visual Studio die Option **Neues Projekt erstellen** aus.

* Wählen Sie **ASP.NET Core-Webanwendung** > **Weiter** aus.

  ![Erstellen eines neuen Projekts für eine ASP.NET Core-Webanwendung](start-mvc/_static/np_2.1.png)

* Geben Sie dem Projekt den Namen **MvcMovie**, und klicken Sie dann auf **Erstellen**. Es ist wichtig, dem Projekt den Namen **MvcMovie** zu geben, damit beim Kopieren von Code der Namespace übereinstimmt.

  ![Neues Projekt konfigurieren](start-mvc/_static/config.png)

* Wählen Sie **Webanwendung (Model View Controller)** aus. Wählen Sie in den Dropdownfeldern **.NET Core** und **ASP.NET Core 3.1** und anschließend **Erstellen** aus.

  ![Dialogfeld „Neues Projekt“, .NET Core im linken Bereich, ASP.NET Core-Web-App ](start-mvc/_static/new_project30.png)

Visual Studio hat die Standardprojektvorlage für das erstellte MVC-Projekt verwendet. Für das erstellte Projekt gilt:

* Es handelt sich um eine funktionierende App.
* Es handelt sich um ein einfaches Startprojekt.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Das Tutorial setzt voraus, dass Sie mit Visual Studio Code vertraut sind. Weitere Informationen finden Sie unter [Erste Schritte mit VS Code](https://code.visualstudio.com/docs) und [Hilfe zu Visual Studio Code](#visual-studio-code-help).

* Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Wechseln Sie mit `cd` zu einem Ordner, der das Projekt enthalten soll.
* Führen Sie den folgenden Befehl aus:

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * Es wird ein Dialogfeld mit folgender Meldung angezeigt: **Die erforderlichen Objekte zum Erstellen und Debuggen sind in "MvcMovie" nicht vorhanden. Hinzufügen?** . Klicken Sie in diesem Fall auf **Ja**.

  * `dotnet new mvc -o MvcMovie`: Erstellt ein neues ASP.NET Core MVC-Projekt im Ordner *MvcMovie*.
  * `code -r MvcMovie`: Lädt die Projektdatei *MvcMovie.csproj* in Visual Studio Code.

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

* Klicken Sie auf **Datei** > **Neue Projektmappe**.

  ![Neue Projektmappe in macOS](start-mvc/_static/new_project_vsmac.png)

* Navigieren Sie in Visual Studio für Mac-Versionen vor Version 8.6 zu **.NET Core** > **App** > **Webanwendung (Model-View-Controller)**  > **Weiter**. Bei Version 8.6 oder höher klicken Sie auf **Web and Console** > **App** > **Webanwendung (Model-View-Controller)**  > **Weiter** (Web und Konsole).

  ![Auswählen der macOS-Web-App-Vorlage](start-mvc/_static/web_app_template_vsmac.png)

* Gehen Sie im Dialogfeld **Neue Webanwendung konfigurieren** folgendermaßen vor:

  * Vergewissern Sie sich, dass **Authentifizierung** auf **Keine Authentifizierung** festgelegt ist.
  * Wenn eine Option zum Auswählen eines **Zielframeworks** angezeigt wird, wählen Sie die neueste 3.x-Version aus.
  * Klicken Sie auf **Weiter**.

* Nennen Sie das Projekt **MvcMovie**, und wählen Sie dann **Erstellen** aus.

  ![Benennen des macOS-Projekts](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a>Ausführen der App

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Drücken Sie STRG+F5, um die App ohne Debuggen auszuführen.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio:

  * Startet [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview).
  * Führt die App aus.

  Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`. Der Standardhostname für Ihren lokalen Computer lautet `localhost`. Wenn Visual Studio ein Webprojekt erstellt, wird ein zufällig ausgewählter Port für den Webserver verwendet.

Das Starten der App ohne Debuggen über STRG+F5 ermöglicht Ihnen Folgendes:

* Nehmen Sie Änderungen am Code vor.
* Speichern Sie die Datei.
* Aktualisieren Sie den Browser, und sehen Sie sich die Codeänderungen an.

Sie können die App über das Menüelement **Debuggen** im Debugmodus oder Nicht-Debugmodus starten:

![Menü „Debuggen“](start-mvc/_static/debug_menu.png)

Sie können die App debuggen, indem Sie die Schaltfläche **IIS Express** auswählen.

![IIS Express](start-mvc/_static/iis_express.png)

Auf der folgenden Abbildung sehen Sie die App:

![Start- oder Indexseite](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Drücken Sie STRG+F5, um die App ohne Debuggen auszuführen.

  [!INCLUDE[](~/includes/trustCertVSC.md)]

  Visual Studio Code:

  * Startet [Kestrel](xref:fundamentals/servers/kestrel).
  * Startet einen Browser.
  * Navigiert zu `https://localhost:5001`.

  Die Adressleiste zeigt `localhost:port:5001` an, nicht `example.com`. Der Standardhostname für Ihren lokalen Computer lautet `localhost`. „Localhost“ dient nur Webanforderungen vom lokalen Computer.

Das Starten der App ohne Debuggen über STRG+F5 ermöglicht Ihnen Folgendes:

* Nehmen Sie Änderungen am Code vor.
* Speichern Sie die Datei.
* Aktualisieren Sie den Browser, und sehen Sie sich die Codeänderungen an.

  ![Start- oder Indexseite](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

* Wählen Sie **Ausführen** > **Ohne Debuggen starten** auf, um die App zu starten.

  Visual Studio für Mac startet den [Kestrel](xref:fundamentals/servers/index#kestrel)-Server und einen Browser und navigiert zu `http://localhost:port`, wobei es sich bei *port* um eine zufällig ausgewählte Portnummer handelt.

[!INCLUDE[](~/includes/trustCertMac.md)]

Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`. Der Standardhostname für Ihren lokalen Computer lautet `localhost`. Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet. Wenn Sie die App ausführen, wird eine andere Portnummer angezeigt.

Sie können die App über das Menü **Ausführen** im Debugmodus oder Nicht-Debugmodus starten.

Auf der folgenden Abbildung sehen Sie die App:

![Start- oder Indexseite](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

Im nächsten Teil dieses Tutorials erfahren Sie mehr über MVC und beginnen mit dem Schreiben von Code.

> [!div class="step-by-step"]
> [Nächste](adding-controller.md)

::: moniker-end
