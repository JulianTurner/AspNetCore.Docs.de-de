---
title: Tools für ASP.NET-Core Blazor
author: guardrex
description: Informieren Sie sich über die Tools, die zum Erstellen von Blazor-Apps verfügbar sind.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2020
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
uid: blazor/tooling
zone_pivot_groups: operating-systems
ms.openlocfilehash: 29f1a1f211688a1edcd31c7230e7216df7c89eef
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97506811"
---
# <a name="tooling-for-aspnet-core-no-locblazor"></a>Tools für ASP.NET-Core Blazor

Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)

::: zone pivot="windows"

1. Installieren Sie die neueste Version von [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) mit der Workload **ASP.NET und Webentwicklung**.

1. Erstellen Sie ein neues Projekt.

1. Klicken Sie auf **Blazor-App**. Klicken Sie auf **Weiter**.

1. Geben Sie im Feld **Projektname** einen Projektnamen ein, oder übernehmen Sie den Standardnamen. Vergewissern Sie sich, dass der Eintrag für den **Speicherort** korrekt ist, oder geben Sie einen Speicherort für das Projekt an. Wählen Sie **Erstellen** aus.

1. Wählen Sie für eine Blazor WebAssembly-Benutzeroberfläche die **Blazor WebAssembly-App-** Vorlage aus. Wählen Sie für eine Blazor Server-Benutzeroberfläche die **Blazor Server-App-** Vorlage aus. Wählen Sie **Erstellen** aus.

   Aktivieren Sie für das gehostete Modell von Blazor WebAssembly das Kontrollkästchen **ASP.NET Core, gehostet**.

   Informationen zu den beiden Hostingmodellen für Blazor, *Blazor WebAssembly* (eigenständig und gehostet) und *Blazor Server* , finden Sie unter <xref:blazor/hosting-models>.

1. Drücken Sie <kbd>STRG</kbd>+<kbd>F5</kbd>, um die App auszuführen.

Weitere Informationen zum Festlegen des ASP.NET Core-HTTPS-Entwicklungszertifikats als vertrauenswürdig finden Sie hier: <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.

::: zone-end

::: zone pivot="linux"

1. Installieren Sie die neueste Version des [.NET Core SDK](https://dotnet.microsoft.com/download). Wenn Sie das SDK zuvor installiert haben, können Sie die installierte Version ermitteln, indem Sie den folgenden Befehl in einer Befehlsshell ausführen:

   ```dotnetcli
   dotnet --version
   ```

1. Installieren Sie die neueste Version von [Visual Studio Code](https://code.visualstudio.com).

1. Installieren Sie die aktuellste [C# für Visual Studio Code-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).

1. Für eine Blazor WebAssembly-Benutzeroberfläche führen Sie den folgenden Befehl in einer Befehlsshell aus:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   Fügen Sie dem Befehl für ein gehostetes Blazor WebAssembly-Modell die Hostingoption (`-ho` oder `--hosted`) hinzu:
   
   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1 -ho
   ```
   
   Für eine Blazor Server-Benutzeroberfläche führen Sie den folgenden Befehl in einer Befehlsshell aus:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   Informationen zu den beiden Hostingmodellen für Blazor, *Blazor WebAssembly* (eigenständig und gehostet) und *Blazor Server* , finden Sie unter <xref:blazor/hosting-models>.

1. Öffnen Sie in Visual Studio Code den Ordner `WebApplication1`.

1. Die IDE fordert an, dass Sie Ressourcen zum Erstellen und Debuggen des Projekts hinzufügen. Wählen Sie **Ja**.

1. Drücken Sie <kbd>STRG</kbd>+<kbd>F5</kbd>, um die App auszuführen.

## <a name="trust-a-development-certificate"></a>Festlegen eines Entwicklungszertifikats als vertrauenswürdig

Es gibt keine zentrale Möglichkeit, ein Zertifikat in Linux als vertrauenswürdig festzulegen. In der Regel wird einer der folgenden Ansätze verwendet:

* Ausschließen der App-URL über die Ausschlussliste des Browsers.
* Festlegen aller selbstsignierten Zertifikate für `localhost` als vertrauenswürdig.
* Hinzufügen des Zertifikats zur Liste der vertrauenswürdigen Zertifikate im Browser.

Weitere Informationen finden Sie in der Dokumentation zu Ihrem Browseranbieter und Ihrer Linux-Distribution.

::: zone-end

::: zone pivot="macos"

1. Installieren Sie [Visual Studio für Mac](https://visualstudio.microsoft.com/vs/mac/).

1. Wählen Sie **Datei** > **Neue Projektmappe** aus, oder erstellen Sie im **Startfenster** ein **Neues Projekt**.

1. Klicken Sie auf der Randleiste auf **Web and Console** > **App** (Web und Konsole).

   Wählen Sie für eine Blazor WebAssembly-Benutzeroberfläche die **Blazor WebAssembly-App-** Vorlage aus. Wählen Sie für eine Blazor Server-Benutzeroberfläche die **Blazor Server-App-** Vorlage aus. Klicken Sie auf **Weiter**.

   Informationen zu den beiden Hostingmodellen für Blazor, *Blazor WebAssembly* (eigenständig und gehostet) und *Blazor Server* , finden Sie unter <xref:blazor/hosting-models>.

1. Vergewissern Sie sich, dass **Authentifizierung** auf **Keine Authentifizierung** festgelegt ist. Klicken Sie auf **Weiter**.

1. Aktivieren Sie für das gehostete Modell von Blazor WebAssembly das Kontrollkästchen **ASP.NET Core, gehostet**.

1. Benennen Sie im Feld **Projektname** die App `WebApplication1`. Wählen Sie **Erstellen** aus.

1. Wählen Sie **Ausführen** > **Ohne Debuggen starten** aus, um die App *ohne Debugger* auszuführen. Führen Sie die App entweder über **Ausführen** > **Debuggen starten** oder über die Schaltfläche „Ausführen“ (&#9654;) *mit dem Debugger aus*.

Wenn eine Eingabeaufforderung dem Entwicklungszertifikat zu vertrauen scheint, vertrauen Sie dem Zertifikat und fahren Sie fort. Das Benutzer- und Keychainkennwort sind erforderlich, um dem Zertifikat zu vertrauen. Weitere Informationen zum Festlegen des ASP.NET Core-HTTPS-Entwicklungszertifikats als vertrauenswürdig finden Sie hier: <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.

::: zone-end

## <a name="use-visual-studio-code-for-cross-platform-no-locblazor-development"></a>Verwenden von Visual Studio Code für die plattformübergreifende Blazor-Entwicklung

[Visual Studio Code](https://code.visualstudio.com/) ist eine plattformübergreifende Open-Source-Entwicklungsumgebung (Integrated Development Environment, IDE), mit der Blazor-Apps entwickelt werden können. Verwenden Sie die .NET-CLI zum Erstellen einer neuen Blazor-App für die Entwicklung mit Visual Studio Code. Weitere Informationen finden Sie in der [Linux-Version dieses Artikels](/aspnet/core/blazor/tooling?pivots=linux).

## <a name="no-locblazor-template-options"></a>Blazor-Vorlagenoptionen

Das Blazor-Framework bietet für beide Blazor-Hostingmodelle Vorlagen zum Erstellen neuer Apps. Mithilfe der Vorlagen können neue Blazor-Projekte und -Projektmappen erstellt werden, unabhängig von den Tools, die Sie für die Blazor-Entwicklung auswählen (Visual Studio, Visual Studio für Mac, Visual Studio Code oder die .NET-CLI):

* Blazor WebAssembly-Projektvorlage: `blazorwasm`
* Blazor Server-Projektvorlage: `blazorserver`

Weitere Informationen zur Blazor-Hostingmodellen finden Sie unter <xref:blazor/hosting-models>.

Vorlagenoptionen sind verfügbar, indem Sie die Hilfeoption (`-h` oder `--help`) in einer Befehlsshell an den CLI-Befehl [`dotnet new`](/dotnet/core/tools/dotnet-new) übergeben:

```dotnetcli
dotnet new blazorwasm --h
dotnet new blazorserver --h
```
