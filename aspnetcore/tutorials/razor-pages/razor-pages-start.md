---
title: 'Tutorial: Erste Schritte mit Razor Pages in ASP.NET Core'
author: rick-anderson
description: Diese Reihe von Tutorials zeigt, wie Sie Razor Pages in ASP.NET Core verwenden können. Erfahren Sie, wie Sie ein Modell erstellen, Code für Razor-Seiten generieren, Entity Framework Core und SQL Server für den Datenzugriff verwenden, Suchfunktionen hinzufügen, Eingabeüberprüfung hinzufügen und Migrationen verwenden, um das Modell zu aktualisieren.
ms.author: riande
ms.date: 11/12/2019
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
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: ab890b956b1242f183054b7ab4575a59072b4f50
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060234"
---
# <a name="tutorial-get-started-with-no-locrazor-pages-in-aspnet-core"></a>Tutorial: Erste Schritte mit Razor Pages in ASP.NET Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"
Dies ist das erste Tutorial einer Reihe, in der Sie Grundlegendes zur Erstellung einer ASP.NET Core-Web-App mit Razor Pages erfahren.

[!INCLUDE[](~/includes/advancedRP.md)]

Am Ende der Reihe verfügen Sie über eine App, mit der eine Filmdatenbank verwaltet werden kann.  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

In diesem Tutorial:

> [!div class="checklist"]
> * Sie erstellen eine Razor Pages-Web-App.
> * Führen Sie die App aus.
> * Überprüfen Sie die Projektdateien.

Am Ende dieses Tutorials verfügen Sie über eine funktionsfähige Razor Pages-Web-App, auf der Sie in späteren Tutorials aufbauen werden.

![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)

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

* Klicken Sie in der Dropdownliste nacheinander auf **ASP.NET Core 3.1** , **Webanwendung** und **Erstellen**.

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

  Ein *VSCODE* -Verzeichnis, das die Dateien *launch.json* und *tasks.json* enthält, wird dem Stammverzeichnis des Projekts hinzugefügt.

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

* Klicken Sie auf **Datei** > **Neue Projektmappe**.

  ![Neue Projektmappe in macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* Navigieren Sie in Visual Studio für Mac-Versionen vor Version 8.6 zu **.NET Core** > **App** > **Webanwendung** > **Weiter**. Bei Version 8.6 oder höher klicken Sie auf **Web and Console** > **App** > **Webanwendung** > **Weiter** (Web und Konsole).

  ![Auswählen der macOS-Web-App-Vorlage](razor-pages-start/_static/web_app_template_vsmac.png)

* Gehen Sie im Dialogfeld **Neue Webanwendung konfigurieren** folgendermaßen vor:

  * Vergewissern Sie sich, dass **Authentifizierung** auf **Keine Authentifizierung** festgelegt ist.
  * Wenn eine Option zum Auswählen eines **Zielframeworks** angezeigt wird, wählen Sie die neueste 3.x-Version aus.

  Klicken Sie auf **Weiter**.

* Nennen Sie das Projekt **RazorPagesMovie** , und klicken Sie auf **Erstellen**.

  ![Benennen des macOS-Projekts](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Ausführen der App

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a>Überprüfen der Projektdateien

Es folgt eine Übersicht über die Hauptprojektordner und -dateien, mit denen Sie in späteren Tutorials arbeiten werden.

### <a name="pages-folder"></a>Ordner „Seiten“

Enthält Razor-Seiten und unterstützende Dateien. Jede Razor-Seite besteht aus einem Dateienpaar:

* Eine *.cshtml* -Datei, die HTML-Markup mit C#-Code in Razor-Syntax enthält
* Eine *.cshtml.cs* -Datei mit C# Code, in dem Seitenereignisse verarbeitet werden

Unterstützende Dateien haben Namen, die mit einem Unterstrich beginnen. Zum Beispiel sind in der Datei *_Layout.cshtml* Benutzeroberflächenelemente konfiguriert, die für alle Seiten gelten. Mit dieser Datei werden das Navigationsmenü oben auf der Seite und der Urheberrechtshinweis unten auf der Seite eingerichtet. Weitere Informationen finden Sie unter <xref:mvc/views/layout>.

### <a name="wwwroot-folder"></a>Ordner „wwwroot“

Enthält statische Dateien, z. B. HTML-Dateien, JavaScript-Dateien und CSS-Dateien. Weitere Informationen finden Sie unter <xref:fundamentals/static-files>.

### <a name="appsettingsjson"></a>appsettings.json

Enthält Konfigurationsdaten, z. B. Verbindungszeichenfolgen. Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.

### <a name="programcs"></a>Program.cs

Enthält den Einstiegspunkt für das Programm. Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host>.

### <a name="startupcs"></a>Startup.cs

Diese Datei enthält Code, mit dem das App-Verhalten konfiguriert wird. Weitere Informationen finden Sie unter <xref:fundamentals/startup>.

## <a name="next-steps"></a>Nächste Schritte

Wechseln Sie zum nächsten Tutorial in der Reihe:

> [!div class="step-by-step"]
> [Hinzufügen eines Modells](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

Dies ist das erste Tutorial aus einer Reihe. [In der Reihe](xref:tutorials/razor-pages/index) lernen Sie die Grundlagen zum Erstellen einer Razor Pages-Web-App mit ASP.NET Core.

[!INCLUDE[](~/includes/advancedRP.md)]

Am Ende der Reihe verfügen Sie über eine App, mit der eine Filmdatenbank verwaltet werden kann.  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

In diesem Tutorial:

> [!div class="checklist"]
> * Sie erstellen eine Razor Pages-Web-App.
> * Führen Sie die App aus.
> * Überprüfen Sie die Projektdateien.

Am Ende dieses Tutorials verfügen Sie über eine funktionsfähige Razor Pages-Web-App, auf der Sie in späteren Tutorials aufbauen werden.

![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)

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

* Klicken Sie in der Dropdownliste auf **ASP.NET Core 2.2** , auf **Webanwendung** und anschließend auf **Erstellen**.

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

  Ein *VSCODE* -Verzeichnis, das die Dateien *launch.json* und *tasks.json* enthält, wird dem Stammverzeichnis des Projekts hinzugefügt.

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

* Klicken Sie auf **Datei** > **Neue Projektmappe**.

![Neue Projektmappe in macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* Navigieren Sie in Visual Studio für Mac-Versionen vor Version 8.6 zu **.NET Core** > **App** > **Webanwendung** > **Weiter**. Bei Version 8.6 oder höher klicken Sie auf **Web and Console** > **App** > **Webanwendung** > **Weiter** (Web und Konsole).

* Gehen Sie im Dialogfeld **Neue Webanwendung konfigurieren** folgendermaßen vor:

  * Vergewissern Sie sich, dass **Authentifizierung** auf **Keine Authentifizierung** festgelegt ist.
  * Wenn eine Option zum Auswählen eines **Zielframeworks** angezeigt wird, wählen Sie die neueste 2.x-Version aus.

  Klicken Sie auf **Weiter**.

* Nennen Sie das Projekt **RazorPagesMovie** , und klicken Sie auf **Erstellen**.

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Ausführen der App

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Drücken Sie STRG+F5, um die Ausführung ohne den Debugger zu starten.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio startet [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) und führt die App aus. Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`. Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt. „Localhost“ dient nur Webanforderungen vom lokalen Computer. Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.

* Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.

  Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.

  ![Start- oder Indexseite](razor-pages-start/_static/homeGDPR2.2.png)

  Die folgende Abbildung zeigt die App, nachdem Sie das Einverständnis zur Nachverfolgung gegeben haben:

  ![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* Drücken Sie **STRG+F5** , um die Ausführung ohne den Debugger zu starten.

  Visual Studio Code startet [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`. Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`. Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt. „Localhost“ dient nur Webanforderungen vom lokalen Computer.

* Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.

  Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.

  ![Start- oder Indexseite](razor-pages-start/_static/homeGDPR2.2.png)

  Die folgende Abbildung zeigt die App, nachdem Sie das Einverständnis zur Nachverfolgung gegeben haben:

  ![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* Drücken Sie **CMD+OPT+F5** für eine Ausführung ohne Debugger.

  Visual Studio startet dann [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`.

* Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.

  Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.

  ![Start- oder Indexseite](razor-pages-start/_static/homeGDPR2.2_safari.png)

  Die folgende Abbildung zeigt die App, nachdem Sie das Einverständnis zur Nachverfolgung gegeben haben:

  ![Start- oder Indexseite](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a>Überprüfen der Projektdateien

Es folgt eine Übersicht über die Hauptprojektordner und -dateien, mit denen Sie in späteren Tutorials arbeiten werden.

### <a name="pages-folder"></a>Ordner „Seiten“

Enthält Razor-Seiten und unterstützende Dateien. Jede Razor-Seite besteht aus einem Dateienpaar:

* Eine *.cshtml* -Datei, die HTML-Markup mit C#-Code in Razor-Syntax enthält
* Eine *.cshtml.cs* -Datei mit C# Code, in dem Seitenereignisse verarbeitet werden

Unterstützende Dateien haben Namen, die mit einem Unterstrich beginnen. Zum Beispiel sind in der Datei *_Layout.cshtml* Benutzeroberflächenelemente konfiguriert, die für alle Seiten gelten. Mit dieser Datei werden das Navigationsmenü oben auf der Seite und der Urheberrechtshinweis unten auf der Seite eingerichtet. Weitere Informationen finden Sie unter <xref:mvc/views/layout>.

### <a name="wwwroot-folder"></a>Ordner „wwwroot“

Enthält statische Dateien, z. B. HTML-Dateien, JavaScript-Dateien und CSS-Dateien. Weitere Informationen finden Sie unter <xref:fundamentals/static-files>.

### <a name="appsettingsjson"></a>appsettings.json

Enthält Konfigurationsdaten, z. B. Verbindungszeichenfolgen. Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.

### <a name="programcs"></a>Program.cs

Enthält den Einstiegspunkt für das Programm. Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host>.

### <a name="startupcs"></a>Startup.cs

Enthält Code, mit dem das App-Verhalten konfiguriert wird, beispielsweise, ob die App Zustimmung für cookies erfordert. Weitere Informationen finden Sie unter <xref:fundamentals/startup>.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Dieses Tutorials auf YouTube](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a>Nächste Schritte

Wechseln Sie zum nächsten Tutorial in der Reihe:

> [!div class="step-by-step"]
> [Hinzufügen eines Modells](xref:tutorials/razor-pages/model)

::: moniker-end
