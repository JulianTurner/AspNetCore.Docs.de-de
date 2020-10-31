---
title: Erste Schritte mit ASP.NET Core
author: rick-anderson
description: Ein kurzes Tutorial, in dem eine einfache Hallo Welt-App mit ASP.NET Core erstellt und ausgeführt wird.
ms.author: riande
ms.custom: mvc
ms.date: 01/07/2020
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
uid: getting-started
ms.openlocfilehash: fef5ae525a7c01d0ea7733e990233f413aac61a7
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057790"
---
# <a name="tutorial-get-started-with-aspnet-core"></a>Tutorial: Erste Schritte mit ASP.NET Core

Dieses Tutorial zeigt, wie Sie eine ASP.NET Core-Web-App über die .NET Core-CLI erstellen und ausführen.

Sie lernen, die folgende Aufgaben auszuführen:

> [!div class="checklist"]
> * Erstellen Sie ein Web-App-Projekt.
> * Vertrauen Sie dem Entwicklungszertifikat.
> * Führen Sie die App aus.
> * Bearbeiten Sie eine Razor-Seite.

Am Schluss werden Sie eine funktionierende Web-App auf Ihrem lokalen Computer besitzen.

![Web-App-Startseite](_static/home-page.png)

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE[](~/includes/3.1-SDK.md)]

## <a name="create-a-web-app-project"></a>Erstellen Sie ein Web-App-Projekt.

Öffnen Sie eine Befehlsshell, und geben Sie den folgenden Befehl ein:

```dotnetcli
dotnet new webapp -o aspnetcoreapp
```

Für den obigen Befehl gilt Folgendes:

* Erstellt eine neue Web-App.  
* Mit dem `-o aspnetcoreapp`-Parameter wird ein Verzeichnis mit dem Namen *aspnetcoreapp* und den Quelldateien für die App erstellt.

### <a name="trust-the-development-certificate"></a>Dem Entwicklungszertifikat vertrauen

Vertrauen Sie dem HTTPS-Entwicklungszertifikat:

# <a name="windows"></a>[Windows](#tab/windows)

```dotnetcli
dotnet dev-certs https --trust
```

Über den vorherigen Befehl wird der folgende Dialog angezeigt:

![Dialogfeld „Sicherheitswarnung“](~/getting-started/_static/cert.png)

Klicken Sie auf **Ja** , wenn Sie zustimmen möchten, dass das Entwicklungszertifikat vertrauenswürdig ist.

# <a name="macos"></a>[macOS](#tab/macos)

```dotnetcli
dotnet dev-certs https --trust
```

Über den vorherigen Befehl wird die folgende Meldung angezeigt:

*Trusting the HTTPS development certificate was requested. Wenn das Zertifikat nicht bereits vertrauenswürdig ist, führen wir den folgenden Befehl aus:*  `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`.

Dieser Befehl fordert Sie möglicherweise zur Eingabe Ihres Kennworts auf, um das Zertifikat in der Keychain für das System zu installieren. Geben Sie Ihr Kennwort ein, wenn Sie die Vertrauenswürdigkeit des Entwicklungszertifikats bestätigen möchten.

# <a name="linux"></a>[Linux](#tab/linux)

Weitere Informationen zum Bestätigen der Vertrauenswürdigkeit eines HTTPS-Entwicklungszertifikats finden Sie in der Dokumentation zu Ihrer Linux-Distribution.

---

Weitere Informationen finden Sie unter [Festlegen des ASP.NET Core-HTTPS-Entwicklungszertifikat als vertrauenswürdig](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).

## <a name="run-the-app"></a>Ausführen der App

Führen Sie die folgenden Befehle aus:

```dotnetcli
cd aspnetcoreapp
dotnet watch run
```

Nachdem die Befehlsshell angibt, dass die App gestartet wurde, müssen Sie zu `https://localhost:5001` wechseln.

## <a name="edit-a-no-locrazor-page"></a>Bearbeiten einer Razor-Seite

Öffnen Sie *Pages/Index.cshtml* , und ändern und speichern Sie die Seite mit dem folgenden hervorgehobenen Markup:

[!code-cshtml[](sample/index.cshtml?highlight=9)]

Navigieren Sie zu `https://localhost:5001`, aktualisieren Sie die Seite, und überprüfen Sie, ob die Änderungen angezeigt werden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie die folgenden Aufgaben ausgeführt werden:

> [!div class="checklist"]
> * Erstellen Sie ein Web-App-Projekt.
> * Vertrauen Sie dem Entwicklungszertifikat.
> * Führen Sie das Projekt aus.
> * Führen Sie eine Änderung durch.

Um mehr über ASP.NET Core zu lernen, machen Sie sich mit dem empfohlenen Lernpfad in der Einführung vertraut:

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>
