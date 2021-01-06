---
title: IIS-Support zum Zeitpunkt der Entwicklung in Visual Studio für ASP.NET Core
author: rick-anderson
description: Informationen zur Unterstützung des Debuggens von ASP.NET Core-Apps, wenn diese mit IIS unter Windows Server ausgeführt werden.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/development-time-iis-support
ms.openlocfilehash: ab892b2cdfa61378ac7328c0380c8a6cffc6d376
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058453"
---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a>IIS-Support zum Zeitpunkt der Entwicklung in Visual Studio für ASP.NET Core

Von [Sourabh Shirhatti](https://twitter.com/sshirhatti)

::: moniker range=">= aspnetcore-3.0"

In diesem Artikel wird die [Visual Studio](https://visualstudio.microsoft.com)-Unterstützung des Debuggens von ASP.NET Core-Apps beschrieben, die mit IIS unter Windows Server ausgeführt werden. Dieses Thema führt Sie durch die Aktivierung dieses Szenarios und Einrichtung eines Projekts.

## <a name="prerequisites"></a>Voraussetzungen

* [Visual Studio für Windows](https://visualstudio.microsoft.com/downloads/)
* Workload **ASP.NET und Webentwicklung**
* Workload **Plattformübergreifende .NET Core-Entwicklung**
* X.509-Sicherheitszertifikat (für HTTPS-Unterstützung)

## <a name="enable-iis"></a>Aktivieren von IIS

1. Navigieren Sie in Windows zu **Systemsteuerung** > **Programme** > **Programme und Features** > **Windows-Features aktivieren oder deaktivieren** (links auf dem Bildschirm).
1. Aktivieren Sie das Kontrollkästchen **Internetinformationsdienste**. Klicken Sie auf **OK**.

Für die IIS-Installation ist möglicherweise ein Neustart des Systems erforderlich.

## <a name="configure-iis"></a>Konfigurieren von IIS

Für IIS muss eine Website mit Folgendem konfiguriert sein:

* **Hostname:** In der Regel wird die **Standardwebsite** mit dem **Hostnamen** `localhost` verwendet. Allerdings funktioniert jede gültige IIS-Website mit einem eindeutigen Hostnamen.
* **Sitebindung**
  * Für Anwendungen, die HTTPS benötigen, erstellen Sie mithilfe eines Zertifikats eine Bindung an Port 443. In der Regel wird das **IIS Express-Entwicklungszertifikat** verwendet, doch es funktioniert auch jedes andere gültige Zertifikat.
  * Bei Apps, die HTTP verwenden, bestätigen Sie das Vorhandensein einer Bindung an Port 80, oder erstellen Sie für eine neue Website eine Bindung an Port 80.
  * Verwenden Sie eine einzelne Bindung für entweder HTTP oder HTTPS. **Die gleichzeitige Bindung sowohl an HTTP- als auch an HTTPS-Ports wird nicht unterstützt.**

## <a name="enable-development-time-iis-support-in-visual-studio"></a>Aktivieren von IIS-Unterstützung in Visual Studio zur Entwicklungszeit

1. Starten Sie den Visual Studio-Installer.
1. Klicken Sie für die Visual Studio-Installation, die Sie für die Unterstützung von IIS-Entwicklungszeit verwenden möchten, auf **Ändern**.
1. Wechseln Sie für die Workload **ASP.NET- und Webentwicklung** zur Komponente **Entwicklungszeit-IIS-Unterstützung** , und installieren Sie sie.

   Die Komponente befindet sich im Abschnitt **Optional** unter **Entwicklungszeit-IIS-Unterstützung** im Bereich **Installationsdetails** rechts neben den Workloads. Sie installiert das [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) – ein natives IIS-Modul, das zur Ausführung von ASP.NET Core-Anwendungen mit IIS erforderlich ist.

## <a name="configure-the-project"></a>Konfigurieren des Projekts

### <a name="https-redirection"></a>HTTPS-Umleitung

Aktivieren Sie für ein neues Projekt, das HTTPS erfordert, im Fenster **Neue ASP.NET Core-Webanwendung** das Kontrollkästchen neben **Configure for HTTPS** (Für HTTPS konfigurieren). Bei Aktivieren des Kontrollkästchens werden die [HTTPS-Umleitung und HSTS-Middleware](xref:security/enforcing-ssl) der App bei ihrer Erstellung hinzugefügt.

Verwenden Sie für ein vorhandenes Projekt, das HTTPS erfordert, die HTTPS-Umleitung und HSTS-Middleware in `Startup.Configure`. Weitere Informationen finden Sie unter <xref:security/enforcing-ssl>.

Für ein Projekt, das HTTP verwendet, werden die [HTTPS-Umleitung und HSTS-Middleware](xref:security/enforcing-ssl) nicht der App hinzugefügt. Es ist keine App-Konfiguration erforderlich.

### <a name="iis-launch-profile"></a>IIS-Startprofil

So erstellen Sie ein neues Startprofil, um IIS-Unterstützung zur Entwicklungszeit hinzuzufügen:

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt. Klicken Sie auf **Eigenschaften**. Öffnen Sie die Registerkarte **Debuggen**.
1. Klicken Sie für **Profil** auf die Schaltfläche **Neu**. Geben Sie dem Profil im Popupfenster den Namen „IIS“. Klicken Sie auf **OK**, um das Profil zu erstellen.
1. Wählen Sie bei der Einstellung **Start** den Eintrag **IIS** aus der Liste aus.
1. Aktivieren Sie das Kontrollkästchen bei **Browser starten**, und geben Sie die Endpunkt-URL an.

   Wenn die App HTTPS erfordert, verwenden Sie einen HTTPS-Endpunkt (`https://`). Verwenden Sie für HTTP einen HTTP-Endpunkt (`http://`).

   Geben Sie den gleichen Hostnamen und Port wie bei der [zuvor angegebenen IIS-Konfiguration](#configure-iis) an, typischerweise `localhost`.

   Geben Sie den Namen der App am Ende der URL an.

   `https://localhost/WebApplication1` (HTTPS) oder `http://localhost/WebApplication1` (HTTP) sind beispielsweise gültige Endpunkt-URLs.
1. Klicken Sie im Abschnitt **Umgebungsvariablen** auf die Schaltfläche **Hinzufügen**. Geben Sie eine Umgebungsvariable mit dem **Namen**`ASPNETCORE_ENVIRONMENT` und dem **Wert**`Development` an.
1. Legen Sie im Bereich **Webservereinstellungen** die **App-URL** auf den gleichen Wert fest, der für die Endpunkt-URL für **Browser starten** verwendet wird.
1. Wählen Sie für die Einstellung **Hostingmodel** in Visual Studio 2019 oder höher **Standard**, um das vom Projekt verwendete Hostingmodell zu nutzen. Wenn das Projekt die `<AspNetCoreHostingModel>`-Eigenschaft in seiner Projektdatei festlegt, wird der Wert der Eigenschaft (`InProcess` oder `OutOfProcess`) verwendet. Wenn die Eigenschaft nicht vorhanden ist, wird das standardmäßige Hostingmodell der App (In-Process) verwendet. Wenn die App eine explizite Einstellung des Hostingmodells erfordert, die sich vom normalen Hostingmodell der App unterscheidet, legen Sie das **Hostingmodell** nach Bedarf entweder auf `In Process` oder `Out Of Process` fest.
1. Speichern Sie den Profilnamen.

Wenn Sie nicht Visual Studio verwenden, können Sie in der App im Ordner *Properties* manuell ein Startprofil zur Datei [launchSettings.json](https://json.schemastore.org/launchsettings) hinzufügen. Im folgenden Beispiel wird das Profil für die Verwendung des HTTPS-Protokolls konfiguriert:

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

Vergewissern Sie sich, dass die Endpunkte `applicationUrl` und `launchUrl` übereinstimmen, und verwenden Sie das gleiche Protokoll wie die IIS-Bindungskonfiguration, entweder HTTP oder HTTPS.

## <a name="run-the-project"></a>Ausführen des Projekts

Führen Sie Visual Studio als Administrator aus:

* Vergewissern Sie sich, dass die Dropdownliste der Buildkonfiguration auf **Debuggen** festgelegt ist.
* Legen Sie die [Schaltfläche „Debugging starten“](/visualstudio/debugger/debugger-feature-tour) auf das Profil **IIS** fest, und wählen Sie zum Starten der App die Schaltfläche aus.

Visual Studio fordert möglicherweise zu einem Neustart auf, wenn das Profil nicht als Administrator ausgeführt wird. Wenn Sie dazu aufgefordert werden, starten Sie Visual Studio neu.

Bei Verwendung eines nicht vertrauenswürdigen Entwicklungszertifikats müssen Sie für den Browser möglicherweise eine Ausnahme für das nicht vertrauenswürdige Zertifikat erstellen.

> [!NOTE]
> Das Debuggen einer Releasebuildkonfiguration mit [Nur mein Code](/visualstudio/debugger/just-my-code) und Compileroptimierungen führt zu einer beeinträchtigten Leistung. Beispielsweise werden Haltepunkte nicht erreicht.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Erste Schritte mit IIS-Manager in IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

In diesem Artikel wird die [Visual Studio](https://visualstudio.microsoft.com)-Unterstützung des Debuggens von ASP.NET Core-Apps beschrieben, die mit IIS unter Windows Server ausgeführt werden. Dieses Thema führt Sie durch die Aktivierung dieses Szenarios und Einrichtung eines Projekts.

## <a name="prerequisites"></a>Voraussetzungen

* [Visual Studio für Windows](https://visualstudio.microsoft.com/downloads/)
* Workload **ASP.NET und Webentwicklung**
* Workload **Plattformübergreifende .NET Core-Entwicklung**
* X.509-Sicherheitszertifikat (für HTTPS-Unterstützung)

## <a name="enable-iis"></a>Aktivieren von IIS

1. Navigieren Sie in Windows zu **Systemsteuerung** > **Programme** > **Programme und Features** > **Windows-Features aktivieren oder deaktivieren** (links auf dem Bildschirm).
1. Aktivieren Sie das Kontrollkästchen **Internetinformationsdienste**. Klicken Sie auf **OK**.

Für die IIS-Installation ist möglicherweise ein Neustart des Systems erforderlich.

## <a name="configure-iis"></a>Konfigurieren von IIS

Für IIS muss eine Website mit Folgendem konfiguriert sein:

* **Hostname:** In der Regel wird die **Standardwebsite** mit dem **Hostnamen** `localhost` verwendet. Allerdings funktioniert jede gültige IIS-Website mit einem eindeutigen Hostnamen.
* **Sitebindung**
  * Für Anwendungen, die HTTPS benötigen, erstellen Sie mithilfe eines Zertifikats eine Bindung an Port 443. In der Regel wird das **IIS Express-Entwicklungszertifikat** verwendet, doch es funktioniert auch jedes andere gültige Zertifikat.
  * Bei Apps, die HTTP verwenden, bestätigen Sie das Vorhandensein einer Bindung an Port 80, oder erstellen Sie für eine neue Website eine Bindung an Port 80.
  * Verwenden Sie eine einzelne Bindung für entweder HTTP oder HTTPS. **Die gleichzeitige Bindung sowohl an HTTP- als auch an HTTPS-Ports wird nicht unterstützt.**

## <a name="enable-development-time-iis-support-in-visual-studio"></a>Aktivieren von IIS-Unterstützung in Visual Studio zur Entwicklungszeit

1. Starten Sie den Visual Studio-Installer.
1. Klicken Sie für die Visual Studio-Installation, die Sie für die Unterstützung von IIS-Entwicklungszeit verwenden möchten, auf **Ändern**.
1. Wechseln Sie für die Workload **ASP.NET- und Webentwicklung** zur Komponente **Entwicklungszeit-IIS-Unterstützung** , und installieren Sie sie.

   Die Komponente befindet sich im Abschnitt **Optional** unter **Entwicklungszeit-IIS-Unterstützung** im Bereich **Installationsdetails** rechts neben den Workloads. Sie installiert das [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) – ein natives IIS-Modul, das zur Ausführung von ASP.NET Core-Anwendungen mit IIS erforderlich ist.

## <a name="configure-the-project"></a>Konfigurieren des Projekts

### <a name="https-redirection"></a>HTTPS-Umleitung

Aktivieren Sie für ein neues Projekt, das HTTPS erfordert, im Fenster **Neue ASP.NET Core-Webanwendung** das Kontrollkästchen neben **Configure for HTTPS** (Für HTTPS konfigurieren). Bei Aktivieren des Kontrollkästchens werden die [HTTPS-Umleitung und HSTS-Middleware](xref:security/enforcing-ssl) der App bei ihrer Erstellung hinzugefügt.

Verwenden Sie für ein vorhandenes Projekt, das HTTPS erfordert, die HTTPS-Umleitung und HSTS-Middleware in `Startup.Configure`. Weitere Informationen finden Sie unter <xref:security/enforcing-ssl>.

Für ein Projekt, das HTTP verwendet, werden die [HTTPS-Umleitung und HSTS-Middleware](xref:security/enforcing-ssl) nicht der App hinzugefügt. Es ist keine App-Konfiguration erforderlich.

### <a name="iis-launch-profile"></a>IIS-Startprofil

So erstellen Sie ein neues Startprofil, um IIS-Unterstützung zur Entwicklungszeit hinzuzufügen:

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt. Klicken Sie auf **Eigenschaften**. Öffnen Sie die Registerkarte **Debuggen**.
1. Klicken Sie für **Profil** auf die Schaltfläche **Neu**. Geben Sie dem Profil im Popupfenster den Namen „IIS“. Klicken Sie auf **OK**, um das Profil zu erstellen.
1. Wählen Sie bei der Einstellung **Start** den Eintrag **IIS** aus der Liste aus.
1. Aktivieren Sie das Kontrollkästchen bei **Browser starten**, und geben Sie die Endpunkt-URL an.

   Wenn die App HTTPS erfordert, verwenden Sie einen HTTPS-Endpunkt (`https://`). Verwenden Sie für HTTP einen HTTP-Endpunkt (`http://`).

   Geben Sie den gleichen Hostnamen und Port wie bei der [zuvor angegebenen IIS-Konfiguration](#configure-iis) an, typischerweise `localhost`.

   Geben Sie den Namen der App am Ende der URL an.

   `https://localhost/WebApplication1` (HTTPS) oder `http://localhost/WebApplication1` (HTTP) sind beispielsweise gültige Endpunkt-URLs.
1. Klicken Sie im Abschnitt **Umgebungsvariablen** auf die Schaltfläche **Hinzufügen**. Geben Sie eine Umgebungsvariable mit dem **Namen**`ASPNETCORE_ENVIRONMENT` und dem **Wert**`Development` an.
1. Legen Sie im Bereich **Webservereinstellungen** die **App-URL** auf den gleichen Wert fest, der für die Endpunkt-URL für **Browser starten** verwendet wird.
1. Wählen Sie für die Einstellung **Hostingmodel** in Visual Studio 2019 oder höher **Standard**, um das vom Projekt verwendete Hostingmodell zu nutzen. Wenn das Projekt die `<AspNetCoreHostingModel>`-Eigenschaft in seiner Projektdatei festlegt, wird der Wert der Eigenschaft (`InProcess` oder `OutOfProcess`) verwendet. Wenn die Eigenschaft nicht vorhanden ist, wird das standardmäßige Hostingmodell der App (Out-of-Process) verwendet. Wenn die App eine explizite Einstellung des Hostingmodells erfordert, die sich vom normalen Hostingmodell der App unterscheidet, legen Sie das **Hostingmodell** nach Bedarf entweder auf `In Process` oder `Out Of Process` fest.
1. Speichern Sie den Profilnamen.

Wenn Sie nicht Visual Studio verwenden, können Sie in der App im Ordner *Properties* manuell ein Startprofil zur Datei [launchSettings.json](https://json.schemastore.org/launchsettings) hinzufügen. Im folgenden Beispiel wird das Profil für die Verwendung des HTTPS-Protokolls konfiguriert:

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

Vergewissern Sie sich, dass die Endpunkte `applicationUrl` und `launchUrl` übereinstimmen, und verwenden Sie das gleiche Protokoll wie die IIS-Bindungskonfiguration, entweder HTTP oder HTTPS.

## <a name="run-the-project"></a>Ausführen des Projekts

Führen Sie Visual Studio als Administrator aus:

* Vergewissern Sie sich, dass die Dropdownliste der Buildkonfiguration auf **Debuggen** festgelegt ist.
* Legen Sie die [Schaltfläche „Debugging starten“](/visualstudio/debugger/debugger-feature-tour) auf das Profil **IIS** fest, und wählen Sie zum Starten der App die Schaltfläche aus.

Visual Studio fordert möglicherweise zu einem Neustart auf, wenn das Profil nicht als Administrator ausgeführt wird. Wenn Sie dazu aufgefordert werden, starten Sie Visual Studio neu.

Bei Verwendung eines nicht vertrauenswürdigen Entwicklungszertifikats müssen Sie für den Browser möglicherweise eine Ausnahme für das nicht vertrauenswürdige Zertifikat erstellen.

> [!NOTE]
> Das Debuggen einer Releasebuildkonfiguration mit [Nur mein Code](/visualstudio/debugger/just-my-code) und Compileroptimierungen führt zu einer beeinträchtigten Leistung. Beispielsweise werden Haltepunkte nicht erreicht.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Erste Schritte mit IIS-Manager in IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end
