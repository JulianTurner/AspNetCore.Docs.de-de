---
title: Hosten und Bereitstellen von ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Sie Hostingumgebungen einrichten und ASP.NET Core-Apps bereitstellen.
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
uid: host-and-deploy/index
ms.openlocfilehash: 4f3d4c29a189cf6aa14eb10f570f0b35d8ff9abc
ms.sourcegitcommit: 92439194682dc788b8b5b3a08bd2184dc00e200b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96556618"
---
# <a name="host-and-deploy-aspnet-core"></a>Hosten und Bereitstellen von ASP.NET Core

::: moniker range=">= aspnetcore-2.2"

Um eine ASP.NET Core-App in einer Hostingumgebung bereitzustellen, müssen Sie allgemein folgende Schritte durchführen:

* Stellen Sie die veröffentlichte App in einem Ordner auf dem Hostserver bereit.
* Richten Sie einen Prozess-Manager ein, der die App startet, wenn Anforderungen eingehen und die App nach einem Absturz oder Serverneustart neu startet.
* Richten Sie zur Konfiguration eines Reverseproxys einen Reverseproxy ein, der Anforderungen an die App weiterleitet.

## <a name="publish-to-a-folder"></a>Veröffentlichen in einem Ordner

Der Befehl [dotnet publish](/dotnet/core/tools/dotnet-publish) kompiliert App-Code und kopiert die Dateien, die zum Ausführen der App erforderlich sind, in den Ordner *publish*. Bei der Bereitstellung von Visual Studio erfolgt der `dotnet publish`-Schritt automatisch, bevor die Dateien in das Bereitstellungsziel kopiert werden.

## <a name="publish-settings-files"></a>Dateien mit Veröffentlichungseinstellungen

`*.json`-Dateien werden standardmäßig veröffentlicht. Um andere Einstellungsdateien zu veröffentlichen, geben Sie sie in einem [`<ItemGroup><Content Include= ... />`](/visualstudio/msbuild/common-msbuild-project-items#content)-Element in der Projektdatei an. Im folgenden Beispiel werden XML-Dateien veröffentlicht:

```xml
<ItemGroup>
  <Content Include="**\*.xml" Exclude="bin\**\*;obj\**\*"
    CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

### <a name="folder-contents"></a>Ordnerinhalte

Der Ordner *publish* enthält mindestens eine App-Assemblydatei, Abhängigkeiten und optional die .NET-Runtime.

Eine .NET Core-App kann als *eigenständige Bereitstellung* oder als *Framework-abhängige Bereitstellung* veröffentlicht werden. Wenn die App eigenständig ist, sind die Assemblydateien, die die .NET-Runtime enthalten, im Ordner *publish* enthalten. Wenn die App frameworkabhängig ist, sind die Dateien für die .NET-Runtime nicht enthalten, da die App über einen Verweis auf eine auf dem Server installierte .NET-Version verfügt. Das Standardmodell für die Bereitstellung ist Framework-abhängig. Weitere Informationen finden Sie unter [.NET Core application deployment (.NET Core-Anwendungsbereitstellung)](/dotnet/core/deploying/).

Zusätzlich zu den *EXE*- und *DLL*-Dateien enthält der Ordner *publish* für eine ASP.NET Core-App üblicherweise noch Konfigurationsdateien, statische Objekte und MVC-Ansichten. Weitere Informationen finden Sie unter <xref:host-and-deploy/directory-structure>.

## <a name="set-up-a-process-manager"></a>Einrichten eines Prozessmanagers

Bei einer ASP.NET Core-App handelt es sich um eine Konsolen-App, die gestartet werden muss, wenn ein Server startet und nach Abstürzen neu startet. Sie benötigen einen Prozess-Manager, um die Starts und Neustarts zu automatisieren. Die gängigsten Prozess-Manager für ASP.NET Core sind Folgende:

* Linux
  * [Nginx](xref:host-and-deploy/linux-nginx)
  * [Apache](xref:host-and-deploy/linux-apache)
* Windows
  * [IIS](xref:host-and-deploy/iis/index)
  * [Windows-Dienst](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a>Einrichten eines Reverseproxys

Wenn die App den [Kestrel](xref:fundamentals/servers/kestrel) Server verwendet, können [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache) oder [IIS](xref:host-and-deploy/iis/index) als Reverseproxyserver verwendet werden. Ein Reverseproxyserver empfängt HTTP-Anforderungen aus dem Internet und leitet diese an Kestrel weiter.

Jede der beiden Konfigurationen &mdash;mit oder ohne einen Reverseproxyserver&mdash; ist eine unterstützte Hostingkonfiguration. Weitere Informationen finden Sie unter [When to use Kestrel with a reverse proxy (Verwenden von Kestrel mit einem Reverseproxy)](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).

## <a name="proxy-server-and-load-balancer-scenarios"></a>Proxyserver und Lastenausgleichsszenarien

Möglicherweise ist zusätzliche Konfiguration für Apps erforderlich, die hinter Proxyservern und Lastenausgleichsmodulen (Load Balancer) gehostet werden. Ohne zusätzliche Konfiguration hat eine App möglicherweise keinen Zugriff auf das Schema (HTTP/HTTPS) und die Remote-IP-Adresse, von der eine Anforderung stammte. Weitere Informationen hierzu feinden Sie unter [Konfigurieren von ASP.NET Core zur Verwendung mit Proxyservern und Lastenausgleich](xref:host-and-deploy/proxy-load-balancer).

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a>Verwenden von Visual Studio und MSBuild zum Automatisieren der Bereitstellungen

Die Bereitstellung erfordert neben dem Kopieren der Ausgabe von [dotnet publish](/dotnet/core/tools/dotnet-publish) auf einen Server oft zusätzliche Aufgaben. Beispielsweise können zusätzliche Dateien aus dem Ordner *publish* erforderlich oder ausgeschlossen sein. Visual Studio verwendet [MSBuild](/visualstudio/msbuild/msbuild) für die Webbereitstellung, und MSBuild kann für die Ausführung vieler weiterer Tasks während der Bereitstellung angepasst werden. Weitere Informationen finden Sie unter <xref:host-and-deploy/visual-studio-publish-profiles> und im Buch [Using MSBuild and Team Foundation Build (Verwenden von MSBuild und Team Foundation Build)](http://msbuildbook.com/).

Mithilfe des [Features zum Veröffentlichen einer Web-App](xref:tutorials/publish-to-azure-webapp-using-vs) oder der [integrierten Git-Unterstützung](xref:host-and-deploy/azure-apps/azure-continuous-deployment) können Apps direkt von Visual Studio in Azure App Service bereitgestellt werden. Azure DevOps Services unterstützt [Continuous Deployment](/azure/devops/pipelines/targets/webapp) in Azure App Service. Weitere Informationen finden Sie unter [DevOps mit ASP.NET Core und Azure](xref:azure/devops/index).

## <a name="publish-to-azure"></a>Veröffentlichen in Azure

Anleitungen zum Veröffentlichen einer App in Azure mithilfe von Visual Studio finden Sie unter <xref:tutorials/publish-to-azure-webapp-using-vs>. Ein weiteres Beispiel wird unter [Erstellen von ASP.NET Core-Web-Apps in Azure](/azure/app-service/app-service-web-get-started-dotnet) bereitgestellt.

## <a name="publish-with-msdeploy-on-windows"></a>Veröffentlichen mit MSDeploy unter Windows

Anleitungen zum Veröffentlichen einer App mit einem Visual Studio-Veröffentlichungsprofil z.B. von einer Windows-Eingabeaufforderung mit dem Befehl [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) finden Sie unter <xref:host-and-deploy/visual-studio-publish-profiles>.

## <a name="internet-information-services-iis"></a>IIS (Internet Information Services)

Informationen zu Bereitstellungen zu Internetinformationsdiensten (IIS) mit von der *web.config*-Datei bereitgestellter Konfiguration, finden Sie in den Artikeln unter <xref:host-and-deploy/iis/index>.

## <a name="host-in-a-web-farm"></a>Hosten in einer Webfarm

Weitere Informationen zur Konfiguration des Hostings von ASP.NET Core-Apps in einer Webfarmumgebung (z.B. Bereitstellen mehrerer App-Instanzen zur besseren Skalierbarkeit) finden Sie unter <xref:host-and-deploy/web-farm>.

## <a name="host-on-docker"></a>Hosten auf Docker

Weitere Informationen finden Sie unter <xref:host-and-deploy/docker/index>.

## <a name="perform-health-checks"></a>Ausführen von Integritätsprüfungen

Verwenden Sie Middleware für die Integritätsprüfung, um Integritätsprüfungen für eine App und deren Abhängigkeiten auszuführen. Weitere Informationen finden Sie unter <xref:host-and-deploy/health-checks>.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:test/troubleshoot>
* [Hosten von ASP.NET](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Um eine ASP.NET Core-App in einer Hostingumgebung bereitzustellen, müssen Sie allgemein folgende Schritte durchführen:

* Stellen Sie die veröffentlichte App in einem Ordner auf dem Hostserver bereit.
* Richten Sie einen Prozess-Manager ein, der die App startet, wenn Anforderungen eingehen und die App nach einem Absturz oder Serverneustart neu startet.
* Richten Sie zur Konfiguration eines Reverseproxys einen Reverseproxy ein, der Anforderungen an die App weiterleitet.

## <a name="publish-to-a-folder"></a>Veröffentlichen in einem Ordner

Der Befehl [dotnet publish](/dotnet/core/tools/dotnet-publish) kompiliert App-Code und kopiert die Dateien, die zum Ausführen der App erforderlich sind, in den Ordner *publish*. Bei der Bereitstellung von Visual Studio erfolgt der `dotnet publish`-Schritt automatisch, bevor die Dateien in das Bereitstellungsziel kopiert werden.

### <a name="folder-contents"></a>Ordnerinhalte

Der Ordner *publish* enthält mindestens eine App-Assemblydatei, Abhängigkeiten und optional die .NET-Runtime.

Eine .NET Core-App kann als *eigenständige Bereitstellung* oder als *Framework-abhängige Bereitstellung* veröffentlicht werden. Wenn die App eigenständig ist, sind die Assemblydateien, die die .NET-Runtime enthalten, im Ordner *publish* enthalten. Wenn die App frameworkabhängig ist, sind die Dateien für die .NET-Runtime nicht enthalten, da die App über einen Verweis auf eine auf dem Server installierte .NET-Version verfügt. Das Standardmodell für die Bereitstellung ist Framework-abhängig. Weitere Informationen finden Sie unter [.NET Core application deployment (.NET Core-Anwendungsbereitstellung)](/dotnet/core/deploying/).

Zusätzlich zu den *EXE*- und *DLL*-Dateien enthält der Ordner *publish* für eine ASP.NET Core-App üblicherweise noch Konfigurationsdateien, statische Objekte und MVC-Ansichten. Weitere Informationen finden Sie unter <xref:host-and-deploy/directory-structure>.

## <a name="set-up-a-process-manager"></a>Einrichten eines Prozessmanagers

Bei einer ASP.NET Core-App handelt es sich um eine Konsolen-App, die gestartet werden muss, wenn ein Server startet und nach Abstürzen neu startet. Sie benötigen einen Prozess-Manager, um die Starts und Neustarts zu automatisieren. Die gängigsten Prozess-Manager für ASP.NET Core sind Folgende:

* Linux
  * [Nginx](xref:host-and-deploy/linux-nginx)
  * [Apache](xref:host-and-deploy/linux-apache)
* Windows
  * [IIS](xref:host-and-deploy/iis/index)
  * [Windows-Dienst](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a>Einrichten eines Reverseproxys

Wenn die App den [Kestrel](xref:fundamentals/servers/kestrel) Server verwendet, können [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache) oder [IIS](xref:host-and-deploy/iis/index) als Reverseproxyserver verwendet werden. Ein Reverseproxyserver empfängt HTTP-Anforderungen aus dem Internet und leitet diese an Kestrel weiter.

Jede der beiden Konfigurationen &mdash;mit oder ohne einen Reverseproxyserver&mdash; ist eine unterstützte Hostingkonfiguration. Weitere Informationen finden Sie unter [When to use Kestrel with a reverse proxy (Verwenden von Kestrel mit einem Reverseproxy)](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).

## <a name="proxy-server-and-load-balancer-scenarios"></a>Proxyserver und Lastenausgleichsszenarien

Möglicherweise ist zusätzliche Konfiguration für Apps erforderlich, die hinter Proxyservern und Lastenausgleichsmodulen (Load Balancer) gehostet werden. Ohne zusätzliche Konfiguration hat eine App möglicherweise keinen Zugriff auf das Schema (HTTP/HTTPS) und die Remote-IP-Adresse, von der eine Anforderung stammte. Weitere Informationen hierzu feinden Sie unter [Konfigurieren von ASP.NET Core zur Verwendung mit Proxyservern und Lastenausgleich](xref:host-and-deploy/proxy-load-balancer).

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a>Verwenden von Visual Studio und MSBuild zum Automatisieren der Bereitstellungen

Die Bereitstellung erfordert neben dem Kopieren der Ausgabe von [dotnet publish](/dotnet/core/tools/dotnet-publish) auf einen Server oft zusätzliche Aufgaben. Beispielsweise können zusätzliche Dateien aus dem Ordner *publish* erforderlich oder ausgeschlossen sein. Visual Studio verwendet MSBuild für die Webbereitstellung, und MSBuild kann für die Ausführung vieler weiterer Tasks während der Bereitstellung angepasst werden. Weitere Informationen finden Sie unter <xref:host-and-deploy/visual-studio-publish-profiles> und im Buch [Using MSBuild and Team Foundation Build (Verwenden von MSBuild und Team Foundation Build)](http://msbuildbook.com/).

Mithilfe des [Features zum Veröffentlichen einer Web-App](xref:tutorials/publish-to-azure-webapp-using-vs) oder der [integrierten Git-Unterstützung](xref:host-and-deploy/azure-apps/azure-continuous-deployment) können Apps direkt von Visual Studio in Azure App Service bereitgestellt werden. Azure DevOps Services unterstützt [Continuous Deployment](/azure/devops/pipelines/targets/webapp) in Azure App Service. Weitere Informationen finden Sie unter [DevOps mit ASP.NET Core und Azure](xref:azure/devops/index).

## <a name="publish-to-azure"></a>Veröffentlichen in Azure

Anleitungen zum Veröffentlichen einer App in Azure mithilfe von Visual Studio finden Sie unter <xref:tutorials/publish-to-azure-webapp-using-vs>. Ein weiteres Beispiel wird unter [Erstellen von ASP.NET Core-Web-Apps in Azure](/azure/app-service/app-service-web-get-started-dotnet) bereitgestellt.

## <a name="publish-with-msdeploy-on-windows"></a>Veröffentlichen mit MSDeploy unter Windows

Anleitungen zum Veröffentlichen einer App mit einem Visual Studio-Veröffentlichungsprofil z.B. von einer Windows-Eingabeaufforderung mit dem Befehl [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) finden Sie unter <xref:host-and-deploy/visual-studio-publish-profiles>.

## <a name="internet-information-services-iis"></a>IIS (Internet Information Services)

Informationen zu Bereitstellungen zu Internetinformationsdiensten (IIS) mit von der *web.config*-Datei bereitgestellter Konfiguration, finden Sie in den Artikeln unter <xref:host-and-deploy/iis/index>.

## <a name="host-in-a-web-farm"></a>Hosten in einer Webfarm

Weitere Informationen zur Konfiguration des Hostings von ASP.NET Core-Apps in einer Webfarmumgebung (z.B. Bereitstellen mehrerer App-Instanzen zur besseren Skalierbarkeit) finden Sie unter <xref:host-and-deploy/web-farm>.

## <a name="host-on-docker"></a>Hosten auf Docker

Weitere Informationen finden Sie unter <xref:host-and-deploy/docker/index>.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:test/troubleshoot>
* [Hosten von ASP.NET](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end
