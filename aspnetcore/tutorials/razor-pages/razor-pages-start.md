---
title: 'Tutorial: Erste Schritte mit Razor Pages in ASP.NET Core'
author: rick-anderson
description: Dies ist das erste Tutorial einer Reihe, in der Sie Grundlegendes zur Erstellung einer ASP.NET Core-Web-App mit Razor Pages erfahren.
ms.author: riande
ms.date: 09/15/2020
ms.custom: contperf-fy21q2
no-loc:
- Index
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
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 4d4e50f8acea73859f5e839616f13f90a42291c4
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97486225"
---
# <a name="tutorial-get-started-with-no-locrazor-pages-in-aspnet-core"></a>Tutorial: Erste Schritte mit Razor Pages in ASP.NET Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-5.0"
Dies ist das erste Tutorial einer Reihe, in der Sie Grundlegendes zur Erstellung einer ASP.NET Core-Web-App mit Razor Pages erfahren.

Eine erweiterte Einführung für Entwickler, die mit Controllern und Ansichten vertraut sind, finden Sie unter [Einführung in Razor Pages](xref:razor-pages/index).

Am Ende der Reihe verfügen Sie über eine App, mit der eine Filmdatenbank verwaltet werden kann.  

[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).

In diesem Tutorial:

> [!div class="checklist"]
> * Sie erstellen eine Razor Pages-Web-App.
> * Führen Sie die App aus.
> * Überprüfen Sie die Projektdateien.

Am Ende dieses Tutorials verfügen Sie über eine funktionsfähige Razor Pages-Web-App, die der Sie in späteren Tutorials weiterentwickeln werden.

![Seite „Home“ oder Index](razor-pages-start/_static/5/home5.png)

## <a name="prerequisites"></a>Voraussetzungen

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a>Erstellen einer Razor Pages-Web-App

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Starten Sie Visual Studio, und wählen Sie **Neues Projekt erstellen** aus. Weitere Informationen finden Sie unter [Erstellen eines neuen Projekts in Visual Studio](/visualstudio/ide/create-new-project).

   ![Erstellen eines neuen Projekts im Startfenster](razor-pages-start/_static/5/start-window-create-new-project.png)

1. Wählen Sie im Dialogfeld **Neues Projekt erstellen** die Option **ASP.NET Core-Webanwendung** aus, und klicken Sie dann auf **Weiter**.

    ![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/5/np.png)
    
1. Geben Sie `RazorPagesMovie` im Dialogfeld **Neues Projekt konfigurieren** für **Projektname** ein. Es ist wichtig, dem Projekt den Namen *RazorPagesMovie* in übereinstimmender Groß-/Kleinschreibung zu geben, damit die Namespaces übereinstimmen, wenn Sie Beispielcode kopieren und einfügen.

1. Klicken Sie auf **Erstellen**.

    ![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/config.png)

1. Wählen Sie im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** Folgendes aus:
    1. In den Dropdownmenüs **.NET Core** und **ASP.NET Core 5.0**.
    1. **Webanwendung**
    1. **Erstellen**:

     ![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/5/npx.png)

    Das folgende Startprojekt wird erstellt:

    ![Projektmappen-Explorer](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).

1. Wechseln Sie zum Verzeichnis (`cd`), das das Projekt enthalten soll.

1. Führen Sie die folgenden Befehle aus:

   ```dotnetcli
   dotnet new webapp -o RazorPagesMovie
   code -r RazorPagesMovie
   ```

   * Der Befehl `dotnet new` erstellt ein neues Razor Pages-Projekt im Ordner *RazorPagesMovie*.
   * Der Befehl `code` öffnet den Ordner *RazorPagesMovie* in der aktuellen Instanz von Visual Studio Code.

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

1. Klicken Sie auf **Datei** > **Neue Projektmappe**.

    ![Neue Projektmappe in macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

1. Navigieren Sie in Visual Studio für Mac-Versionen vor Version 8.6 zu **.NET Core** > **App** > **Webanwendung** > **Weiter**. Bei Version 8.6 oder höher klicken Sie auf **Web and Console** > **App** > **Webanwendung** > **Weiter** (Web und Konsole).

    ![Auswählen der macOS-Web-App-Vorlage](razor-pages-start/_static/web_app_template_vsmac.png)

1. Gehen Sie im Dialogfeld **Neue Webanwendung konfigurieren** folgendermaßen vor:

    1. Vergewissern Sie sich, dass **Authentifizierung** auf **Keine Authentifizierung** festgelegt ist.
    1. Wenn eine Option zum Auswählen eines **Zielframeworks** angezeigt wird, wählen Sie die neueste .NET 5.x-Version aus.
    1. Wählen Sie **Weiter** aus.

1. Nennen Sie das Projekt *RazorPagesMovie*, und wählen Sie **Erstellen** aus.

    ![Benennen des macOS-Projekts](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Ausführen der App

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a>Überprüfen der Projektdateien

Es folgt eine Übersicht über die Hauptprojektordner und -dateien, mit denen Sie in späteren Tutorials arbeiten werden.

### <a name="pages-folder"></a>Ordner „Seiten“

Enthält Razor-Seiten und unterstützende Dateien. Jede Razor-Seite besteht aus einem Dateienpaar:

* Einer *.cshtml*-Datei mit HTML-Markup mit C#-Code in Razor-Syntax.
* Einer *.cshtml.cs*-Datei mit C#-Code zum Verarbeiten von Seitenereignissen.

Unterstützende Dateien haben Namen, die mit einem Unterstrich beginnen. Zum Beispiel sind in der Datei *_Layout.cshtml* Benutzeroberflächenelemente konfiguriert, die für alle Seiten gelten. Mit dieser Datei werden das Navigationsmenü oben auf der Seite und der Urheberrechtshinweis unten auf der Seite eingerichtet. Weitere Informationen finden Sie unter <xref:mvc/views/layout>.

### <a name="wwwroot-folder"></a>Ordner „wwwroot“

Enthält statische Ressourcen, z. B. HTML-, JavaScript- und CSS-Dateien. Weitere Informationen finden Sie unter <xref:fundamentals/static-files>.

### appsettings.json

Enthält Konfigurationsdaten, z. B. Verbindungszeichenfolgen. Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.

### <a name="programcs"></a>Program.cs

Enthält den Einstiegspunkt für die App. Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host>.

### <a name="startupcs"></a>Startup.cs

Diese Datei enthält Code, mit dem das App-Verhalten konfiguriert wird. Weitere Informationen finden Sie unter <xref:fundamentals/startup>.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="step-by-step"]
> [Weiter: Hinzufügen eines Modells](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-5.0" -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

Dies ist das erste Tutorial einer Reihe, in der Sie Grundlegendes zur Erstellung einer ASP.NET Core-Web-App mit Razor Pages erfahren.

Eine erweiterte Einführung für Entwickler, die mit Controllern und Ansichten vertraut sind, finden Sie unter [Einführung in Razor Pages](xref:razor-pages/index).

Am Ende der Reihe verfügen Sie über eine App, mit der eine Filmdatenbank verwaltet werden kann.  

[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).

In diesem Tutorial:

> [!div class="checklist"]
> * Sie erstellen eine Razor Pages-Web-App.
> * Führen Sie die App aus.
> * Überprüfen Sie die Projektdateien.

Am Ende dieses Tutorials verfügen Sie über eine funktionsfähige Razor Pages-Web-App, auf der Sie in späteren Tutorials aufbauen werden.

![Seite „Home“ oder Index](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a>Voraussetzungen

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a>Erstellen einer Razor Pages-Web-App

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.
* Erstellen Sie eine neue ASP.NET Core-Webanwendung, und klicken Sie auf **Weiter**.
  ![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/np_2.1.png)
* Nennen Sie das Projekt **RazorPagesMovie**. Es ist wichtig, den Namen *RazorPagesMovie* zu verwenden, damit die Namespaces übereinstimmen, wenn Sie Code kopieren und einfügen.
  ![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/config.png)

* Klicken Sie in der Dropdownliste nacheinander auf **ASP.NET Core 3.1**, **Webanwendung** und **Erstellen**.

![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/3/npx.png)

  Das folgende Startprojekt wird erstellt:

  ![Projektmappen-Explorer](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).

* Wechseln Sie zum Verzeichnis (`cd`), das das Projekt enthalten soll.

* Führen Sie die folgenden Befehle aus:

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * Der Befehl `dotnet new` erstellt ein neues Razor Pages-Projekt im Ordner *RazorPagesMovie*.
  * Der Befehl `code` öffnet den Ordner *RazorPagesMovie* in der aktuellen Instanz von Visual Studio Code.

* Sobald das OmniSharp-Flammensymbol in der Statusleiste grün angezeigt wird, wird ein Dialogfeld mit folgender Meldung angezeigt: **Die erforderlichen Objekte zum Erstellen und Debuggen sind in "RazorPagesMovie" nicht vorhanden. Sollen sie hinzugefügt werden?** Wählen Sie **Ja**.

  Ein *VSCODE*-Verzeichnis, das die Dateien *launch.json* und *tasks.json* enthält, wird dem Stammverzeichnis des Projekts hinzugefügt.

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

* Klicken Sie auf **Datei** > **Neue Projektmappe**.

  ![Neue Projektmappe in macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* Navigieren Sie in Visual Studio für Mac-Versionen vor Version 8.6 zu **.NET Core** > **App** > **Webanwendung** > **Weiter**. Bei Version 8.6 oder höher klicken Sie auf **Web and Console** > **App** > **Webanwendung** > **Weiter** (Web und Konsole).

  ![Auswählen der macOS-Web-App-Vorlage](razor-pages-start/_static/web_app_template_vsmac.png)

* Gehen Sie im Dialogfeld **Neue Webanwendung konfigurieren** folgendermaßen vor:

  * Vergewissern Sie sich, dass **Authentifizierung** auf **Keine Authentifizierung** festgelegt ist.
  * Wenn eine Option zum Auswählen eines **Zielframeworks** angezeigt wird, wählen Sie die neueste 3.x-Version aus.

  Klicken Sie auf **Weiter**.

* Nennen Sie das Projekt **RazorPagesMovie**, und klicken Sie auf **Erstellen**.

  ![Benennen des macOS-Projekts](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Ausführen der App

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a>Überprüfen der Projektdateien

Es folgt eine Übersicht über die Hauptprojektordner und -dateien, mit denen Sie in späteren Tutorials arbeiten werden.

### <a name="pages-folder"></a>Ordner „Seiten“

Enthält Razor-Seiten und unterstützende Dateien. Jede Razor-Seite besteht aus einem Dateienpaar:

* Einer *.cshtml*-Datei mit HTML-Markup mit C#-Code in Razor-Syntax.
* Einer *.cshtml.cs*-Datei mit C#-Code zum Verarbeiten von Seitenereignissen.

Unterstützende Dateien haben Namen, die mit einem Unterstrich beginnen. Zum Beispiel sind in der Datei *_Layout.cshtml* Benutzeroberflächenelemente konfiguriert, die für alle Seiten gelten. Mit dieser Datei werden das Navigationsmenü oben auf der Seite und der Urheberrechtshinweis unten auf der Seite eingerichtet. Weitere Informationen finden Sie unter <xref:mvc/views/layout>.

### <a name="wwwroot-folder"></a>Ordner „wwwroot“

Enthält statische Dateien, z. B. HTML-, JavaScript- und CSS-Dateien. Weitere Informationen finden Sie unter <xref:fundamentals/static-files>.

### <a name="appsettingsjson"></a>appsettings.json

Enthält Konfigurationsdaten, z. B. Verbindungszeichenfolgen. Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.

### <a name="programcs"></a>Program.cs

Enthält den Einstiegspunkt für das Programm. Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host>.

### <a name="startupcs"></a>Startup.cs

Diese Datei enthält Code, mit dem das App-Verhalten konfiguriert wird. Weitere Informationen finden Sie unter <xref:fundamentals/startup>.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="step-by-step"]
> [Weiter: Hinzufügen eines Modells](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

Dies ist das erste Tutorial aus einer Reihe. [In der Reihe](xref:tutorials/razor-pages/index) lernen Sie die Grundlagen zum Erstellen einer Razor Pages-Web-App mit ASP.NET Core.

Eine erweiterte Einführung für Entwickler, die mit Controllern und Ansichten vertraut sind, finden Sie unter [Einführung in Razor Pages](xref:razor-pages/index).

Am Ende der Reihe verfügen Sie über eine App, mit der eine Filmdatenbank verwaltet werden kann.  

[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).

In diesem Tutorial:

> [!div class="checklist"]
> * Sie erstellen eine Razor Pages-Web-App.
> * Führen Sie die App aus.
> * Überprüfen Sie die Projektdateien.

Am Ende dieses Tutorials verfügen Sie über eine funktionsfähige Razor Pages-Web-App, auf der Sie in späteren Tutorials aufbauen werden.

![Seite „Home“ oder Index](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a>Voraussetzungen

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a>Erstellen einer Razor Pages-Web-App

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.

* Erstellen Sie eine neue ASP.NET Core-Webanwendung, und klicken Sie auf **Weiter**.

  ![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/np_2.1.png)

* Nennen Sie das Projekt **RazorPagesMovie**. Es ist wichtig, den Namen *RazorPagesMovie* zu verwenden, damit die Namespaces übereinstimmen, wenn Sie Code kopieren und einfügen.

  ![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/config.png)

* Klicken Sie in der Dropdownliste auf **ASP.NET Core 2.2**, auf **Webanwendung** und anschließend auf **Erstellen**.

![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/np_2_2.2.png)

  Das folgende Startprojekt wird erstellt:

  ![Projektmappen-Explorer](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).

* Wechseln Sie zum Verzeichnis (`cd`), das das Projekt enthalten soll.

* Führen Sie die folgenden Befehle aus:

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * Der Befehl `dotnet new` erstellt ein neues Razor Pages-Projekt im Ordner *RazorPagesMovie*.
  * Der Befehl `code` öffnet den Ordner *RazorPagesMovie* in der aktuellen Instanz von Visual Studio Code.

* Sobald das OmniSharp-Flammensymbol in der Statusleiste grün angezeigt wird, wird ein Dialogfeld mit folgender Meldung angezeigt: **Die erforderlichen Objekte zum Erstellen und Debuggen sind in "RazorPagesMovie" nicht vorhanden. Sollen sie hinzugefügt werden?** Wählen Sie **Ja**.

  Ein *VSCODE*-Verzeichnis, das die Dateien *launch.json* und *tasks.json* enthält, wird dem Stammverzeichnis des Projekts hinzugefügt.

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

* Klicken Sie auf **Datei** > **Neue Projektmappe**.

![Neue Projektmappe in macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* Navigieren Sie in Visual Studio für Mac-Versionen vor Version 8.6 zu **.NET Core** > **App** > **Webanwendung** > **Weiter**. Bei Version 8.6 oder höher klicken Sie auf **Web and Console** > **App** > **Webanwendung** > **Weiter** (Web und Konsole).

* Gehen Sie im Dialogfeld **Neue Webanwendung konfigurieren** folgendermaßen vor:

  * Vergewissern Sie sich, dass **Authentifizierung** auf **Keine Authentifizierung** festgelegt ist.
  * Wenn eine Option zum Auswählen eines **Zielframeworks** angezeigt wird, wählen Sie die neueste 2.x-Version aus.

  Klicken Sie auf **Weiter**.

* Nennen Sie das Projekt **RazorPagesMovie**, und klicken Sie auf **Erstellen**.

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Ausführen der App

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Drücken Sie STRG+F5, um die Ausführung ohne den Debugger zu starten.

  Das Starten der App mit <kbd>STRG+F5</kbd> (Nicht-Debugmodus) ermöglicht die Änderung des Codes, das Speichern der Datei, das Aktualisieren des Browsers und das Anzeigen von Codeänderungen. Viele Entwickler bevorzugen den Nicht-Debugmodus, um die App schnell zu starten und Änderungen anzuzeigen.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio startet [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) und führt die App aus. Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`. Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt. „Localhost“ dient nur Webanforderungen vom lokalen Computer. Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.

* Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.

  Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.

  ![Seite „Home“ oder Index](razor-pages-start/_static/homeGDPR2.2.png)

  Die folgende Abbildung zeigt die App, nachdem Sie die Zustimmung zur Nachverfolgung gegeben haben:

  ![Seite „Home“ oder Index](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* Drücken Sie <kbd>STRG+F5</kbd>, um die Ausführung ohne Debugger zu starten.

  Das Starten der App mit <kbd>STRG+F5</kbd> (Nicht-Debugmodus) ermöglicht die Änderung des Codes, das Speichern der Datei, das Aktualisieren des Browsers und das Anzeigen von Codeänderungen. Viele Entwickler bevorzugen den Nicht-Debugmodus, um die App schnell zu starten und Änderungen anzuzeigen.

  Visual Studio Code startet [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`. Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`. Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt. „Localhost“ dient nur Webanforderungen vom lokalen Computer.

* Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.

  Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.

  ![Seite „Home“ oder Index](razor-pages-start/_static/homeGDPR2.2.png)

  Die folgende Abbildung zeigt die App, nachdem Sie das Einverständnis zur Nachverfolgung gegeben haben:

  ![Seite „Home“ oder Index](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* Drücken Sie **CMD+OPT+F5** für eine Ausführung ohne Debugger.

  Das Starten der App mit <kbd>Cmd+Opt+F5</kbd> (Nicht-Debugmodus) ermöglicht die Änderung des Codes, das Speichern der Datei, das Aktualisieren des Browsers und das Anzeigen von Codeänderungen. Viele Entwickler bevorzugen den Nicht-Debugmodus, um die App schnell zu starten und Änderungen anzuzeigen.

  Visual Studio startet dann [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`.

* Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.

  Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.

  ![Seite „Home“ oder Index](razor-pages-start/_static/homeGDPR2.2_safari.png)

  Die folgende Abbildung zeigt die App, nachdem Sie die Zustimmung zur Nachverfolgung gegeben haben:

  ![Seite „Home“ oder Index](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a>Überprüfen der Projektdateien

Es folgt eine Übersicht über die Hauptprojektordner und -dateien, mit denen Sie in späteren Tutorials arbeiten werden.

### <a name="pages-folder"></a>Ordner „Seiten“

Enthält Razor-Seiten und unterstützende Dateien. Jede Razor-Seite besteht aus einem Dateienpaar:

* Einer *.cshtml*-Datei mit HTML-Markup mit C#-Code in Razor-Syntax.
* Einer *.cshtml.cs*-Datei mit C#-Code zum Verarbeiten von Seitenereignissen.

Unterstützende Dateien haben Namen, die mit einem Unterstrich beginnen. Zum Beispiel sind in der Datei *_Layout.cshtml* Benutzeroberflächenelemente konfiguriert, die für alle Seiten gelten. Mit dieser Datei werden das Navigationsmenü oben auf der Seite und der Urheberrechtshinweis unten auf der Seite eingerichtet. Weitere Informationen finden Sie unter <xref:mvc/views/layout>.

Razor-Seiten werden von `PageModel` abgeleitet. Gemäß der Konvention wird die von `PageModel` abgeleitete Klasse `<PageName>Model` genannt.

### <a name="wwwroot-folder"></a>Ordner „wwwroot“

Enthält statische Dateien, z. B. HTML-, JavaScript- und CSS-Dateien. Weitere Informationen finden Sie unter <xref:fundamentals/static-files>.

### <a name="appsettingsjson"></a>appsettings.json

Enthält Konfigurationsdaten, z. B. Verbindungszeichenfolgen. Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.

### <a name="programcs"></a>Program.cs

Enthält den Einstiegspunkt für das Programm. Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host>.

### <a name="startupcs"></a>Startup.cs

Enthält Code, mit dem das App-Verhalten konfiguriert wird, beispielsweise, ob die App Zustimmung für cookies erfordert. Weitere Informationen finden Sie unter <xref:fundamentals/startup>.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Dieses Tutorial auf YouTube](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="step-by-step"]
> [Weiter: Hinzufügen eines Modells](xref:tutorials/razor-pages/model)

::: moniker-end
