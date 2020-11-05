---
title: Veröffentlichen einer ASP.NET Core-App in IIS
author: rick-anderson
description: Erfahren Sie, wie eine ASP.NET Core-App auf einem IIS-Server gehostet wird.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/03/2019
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
uid: tutorials/publish-to-iis
ms.openlocfilehash: b3c714ea8e741430df1f70b2df258f1e8f1c7ad5
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060507"
---
# <a name="publish-an-aspnet-core-app-to-iis"></a>Veröffentlichen einer ASP.NET Core-App in IIS

In diesem Tutorial wird gezeigt, wie eine ASP.NET Core-App auf einem IIS-Server gehostet wird.

Dieses Tutorial behandelt die folgenden Themen:

> [!div class="checklist"]
> * Installieren des Pakets „.NET Core Hosting“ unter Windows Server.
> * Erstellen Sie einer IIS-Website in IIS-Manager.
> * Bereitstellen einer ASP.NET Core-App.

## <a name="prerequisites"></a>Voraussetzungen

* [.Net Core SDK](/dotnet/core/sdk) auf dem Entwicklungs Computer.
* Windows Server, konfiguriert mit der Serverrolle **Webserver (IIS)** . Wenn Ihr Server nicht zum Hosten von Websites mit IIS konfiguriert ist, befolgen Sie die Anleitungen im Abschnitt *IIS-Konfiguration* des Artikels <xref:host-and-deploy/iis/index#iis-configuration>, und kehren Sie dann zu diesem Tutorial zurück.

> [!WARNING]
> **Die IIS-Konfiguration und Websitesicherheit beinhalten Konzepte, die in diesem Tutorial nicht behandelt werden.** Lesen Sie den Leitfaden für IIS in der [Microsoft IIS-Dokumentation](https://www.iis.net/) und den [ASP.NET Core-Artikel zum Hosting mit IIS](xref:host-and-deploy/iis/index), bevor Sie Produktions-Apps unter IIS hosten.
>
> Wichtige Szenarien für das IIS-Hosting, die in diesem Tutorial nicht behandelt werden, sind:
>
> * [Erstellen einer Registrierungsstruktur für den ASP.NET Core-Datenschutz](xref:host-and-deploy/iis/index#data-protection)
> * [Konfiguration der Zugriffssteuerungsliste (Access Control List, ACL) des App-Pools](xref:host-and-deploy/iis/index#application-pool-identity)
> * Um sich auf IIS-Bereitstellungskonzepte zu konzentrieren, stellt dieses Tutorial eine Anwendung ohne in IIS konfigurierte HTTPS-Sicherheit bereit. Weitere Informationen zum Hosting einer App, die für das HTTPS-Protokoll aktiviert ist, finden Sie in den Sicherheitsthemen im Abschnitt [Weitere Ressourcen](#additional-resources) in diesem Artikel. Weitere Anleitungen zum Hosting von ASP.NET Core-Apps finden Sie im Artikel <xref:host-and-deploy/iis/index>.

## <a name="install-the-net-core-hosting-bundle"></a>Installieren des .NET Core Hosting-Pakets

Installieren Sie das *Paket „.NET Core Hosting“* auf dem IIS-Server. Das Paket installiert die .NET Core-Runtime, die .NET Core-Bibliothek und das [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module). Mit dem Modul können ASP.NET Core-Apps hinter IIS ausgeführt werden.

Laden Sie den Installer über folgenden Link herunter:

[Aktueller Installer für das .NET Core Hosting-Paket (direkter Download)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

1. Führen Sie das Installationsprogramm auf dem IIS-Server aus.

1. Starten Sie den Server neu, oder führen Sie `net stop was /y`, gefolgt von `net start w3svc` in einer Befehlsshell aus.

## <a name="create-the-iis-site"></a>Erstellen der IIS-Website

1. Erstellen Sie auf dem IIS-Server einen Ordner zum Speichern der veröffentlichten Ordner und Dateien der App. In einem späteren Schritt wird der Ordnerpfad als physischer App-Pfad in IIS angegeben. Weitere Informationen zu Bereitstellungsordner und Dateilayout einer App finden Sie unter <xref:host-and-deploy/directory-structure>.

1. Öffnen Sie in IIS-Manager den Serverknoten im Bereich **Verbindungen**. Klicken Sie mit der rechten Maustaste auf den Ordner **Websites**. Klicken Sie im Kontextmenü auf **Website hinzufügen**.

1. Geben Sie einen **Websitenamen** an, und legen Sie den **physischen Pfad** auf den erstellten Bereitstellungsordner der App fest. Geben Sie die Konfiguration unter **Bindung** an, und erstellen Sie die Website, indem Sie auf **OK** klicken.

   > [!WARNING]
   > Allgemeine Platzhalterbindungen (`http://*:80/` und `http://+:80`) dürfen **nicht** verwendet werden. Platzhalterbindungen auf oberster Ebene gefährden die Sicherheit Ihrer App. Dies gilt für starke und schwache Platzhalter. Verwenden Sie statt Platzhaltern explizite Hostnamen. Platzhalterbindungen in untergeordneten Domänen (z.B. `*.mysub.com`) verursachen kein Sicherheitsrisiko, wenn Sie die gesamte übergeordnete Domäne steuern (im Gegensatz zu `*.com`, das angreifbar ist). Weitere Informationen finden Sie unter [rfc7230 im Abschnitt 5.4](https://tools.ietf.org/html/rfc7230#section-5.4).

1. Vergewissern Sie sich, dass die Prozessmodellidentität über die richtigen Berechtigungen verfügt.

   Wenn die Standardidentität des App-Pools ( **Prozessmodell** >  **Identity** ) von `ApplicationPoolIdentity` in eine andere Identität geändert wird, sollten Sie sicherstellen, dass die neue Identität über die erforderlichen Berechtigungen zum Zugriff auf den Ordner, die Datenbank und andere erforderliche Ressourcen der App verfügt. Der App-Pool benötigt beispielsweise Lese- und Schreibzugriff für Ordner, in denen die App Lese- und Schreibvorgänge für Dateien ausführt.

## <a name="create-an-aspnet-core-no-locrazor-pages-app"></a>Erstellen einer ASP.NET Core Razor Pages-App

Befolgen Sie das Tutorial <xref:getting-started> zum Erstellen einer Razor Pages-App.

## <a name="publish-and-deploy-the-app"></a>Veröffentlichen und Bereitstellen der App

*Veröffentlichen einer App* bedeutet, dass eine kompilierte App erstellt wird, die von einem Server gehostet werden kann. *Bereitstellen einer App* bedeutet, dass die veröffentlichte App auf ein Hostingsystem verschoben wird. Der Veröffentlichungsschritt wird vom [.NET Core SDK](/dotnet/core/sdk) ausgeführt, während der Bereitstellungsschritt mit einer Vielzahl von Ansätzen ausgeführt werden kann. In diesem Tutorial wird der Bereitstellungsansatz über einen *Ordner* verwendet. Dabei gilt:
 
* Die App wird in einem Ordner veröffentlicht.
* Der Inhalt des Ordners wird in den Ordner der IIS-Website verschoben (der **physische Pfad** zur Website in IIS-Manager).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen** aus.
1. Wählen Sie im Dialogfeld **Veröffentlichungsziel auswählen** die Veröffentlichungsoptionen **Ordner** aus.
1. Legen Sie den Pfad **des Ordners oder der Dateifreigabe** fest.
   * Wenn Sie einen Ordner für die IIS-Website erstellt haben, der auf dem Entwicklungscomputer als Netzwerkfreigabe verfügbar ist, geben Sie den Pfad zur Freigabe an. Der aktuelle Benutzer muss über Schreibzugriff verfügen, um auf der Freigabe veröffentlichen zu können.
   * Wenn Sie nicht in der Lage sind, die Bereitstellung direkt im Ordner der IIS-Website auf dem IIS-Server auszuführen, veröffentlichen Sie diese in einem Ordner auf entfernbaren Medien, und verschieben Sie die veröffentlichte App physisch in den Ordner der IIS-Website auf dem Server. Dies ist der **physische Pfad** der Website in IIS-Manager. Verschieben Sie den Inhalt des Ordners `bin/Release/{TARGET FRAMEWORK}/publish` in den Ordner der IIS-Website auf dem Server, der der **physische Pfad** der Website in IIS Manager ist.

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

1. Veröffentlichen Sie die App mit der Konfiguration „Release“ mit dem Befehl [dotnet publish](/dotnet/core/tools/dotnet-publish) in einer Befehlsshell:

   ```dotnetcli
   dotnet publish --configuration Release
   ```

1. Verschieben Sie den Inhalt des Ordners `bin/Release/{TARGET FRAMEWORK}/publish` in den Ordner der IIS-Website auf dem Server, der der **physische Pfad** der Website in IIS Manager ist.

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

1. Klicken Sie in der **Projektmappe** mit der rechten Maustaste auf das Projekt, und wählen Sie dann **Veröffentlichen** > **In Ordner veröffentlichen** aus.
1. Legen Sie den Pfad unter **Pfad auswählen** fest.
   * Wenn Sie einen Ordner für die IIS-Website erstellt haben, der auf dem Entwicklungscomputer als Netzwerkfreigabe verfügbar ist, geben Sie den Pfad zur Freigabe an. Der aktuelle Benutzer muss über Schreibzugriff verfügen, um auf der Freigabe veröffentlichen zu können.
   * Wenn Sie nicht in der Lage sind, die Bereitstellung direkt im Ordner der IIS-Website auf dem IIS-Server auszuführen, veröffentlichen Sie in einem Ordner auf entfernbaren Medien, und verschieben Sie die veröffentlichte App physisch in den Ordner der IIS-Website auf dem Server. Dies ist der **physische Pfad** der Website in IIS Manager. Verschieben Sie den Inhalt des Ordners `bin/Release/{TARGET FRAMEWORK}/publish` in den Ordner der IIS-Website auf dem Server, der der **physische Pfad** der Website in IIS Manager ist.

---

## <a name="browse-the-website"></a>Navigieren auf der Website

Auf die App kann in einem Browser zugegriffen werden, nachdem die erste Anforderung empfangen wurde. Nehmen Sie eine Anforderung an die App an der Endpunktbindung vor, die Sie in IIS-Manager für die Website eingerichtet haben.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie die folgenden Aufgaben ausgeführt werden:

> [!div class="checklist"]
> * Installieren des Pakets „.NET Core Hosting“ unter Windows Server.
> * Erstellen Sie einer IIS-Website in IIS-Manager.
> * Bereitstellen einer ASP.NET Core-App.

Weitere Informationen zum Hosting von ASP.NET Core-Apps unter IIS finden Sie im IIS-Übersichtsartikel:

> [!div class="nextstepaction"]
> <xref:host-and-deploy/iis/index>

## <a name="additional-resources"></a>Zusätzliche Ressourcen

### <a name="articles-in-the-aspnet-core-documentation-set"></a>Artikel in der ASP.NET Core-Dokumentation

* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:test/troubleshoot-azure-iis>
* <xref:security/enforcing-ssl>

### <a name="articles-pertaining-to-aspnet-core-app-deployment"></a>Artikel zur Bereitstellung von ASP.NET Core-Apps

* <xref:tutorials/publish-to-azure-webapp-using-vs>
* <xref:tutorials/publish-to-azure-webapp-using-vscode>
* <xref:host-and-deploy/visual-studio-publish-profiles>
* [Veröffentlichen einer Web-App in einem Ordner mithilfe von Visual Studio für Mac](/visualstudio/mac/publish-folder)

### <a name="articles-on-iis-https-configuration"></a>Artikel zur IIS-HTTPS-Konfiguration

* [Konfigurieren von SSL in IIS-Manager](/iis/manage/configuring-security/configuring-ssl-in-iis-manager)
* [Einrichten von SSL unter IIS](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)

### <a name="articles-on-iis-and-windows-server"></a>Artikel zu IIS und Windows Server

* [Die offizielle Microsoft IIS-Website](https://www.iis.net/)
* [Bibliothek mit technischem Inhalt zu Windows Server](/windows-server/windows-server)

### <a name="deployment-resources-for-iis-administrators"></a>Bereitstellungsressourcen für IIS-Administratoren

* [IIS-Dokumentation](/iis)
* [Erste Schritte mit IIS-Manager in IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [.NET Core-Anwendungsbereitstellung](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

