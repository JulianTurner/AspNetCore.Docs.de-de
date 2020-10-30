---
title: Veröffentlichen einer ASP.net Core- SignalR app in Azure App Service
author: bradygaster
description: Erfahren Sie, wie Sie eine ASP.net Core- SignalR app in Azure App Service veröffentlichen.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
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
uid: signalr/publish-to-azure-web-app
ms.openlocfilehash: e00eea81788c9b335691b7e5ffe6a46534c3c492
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058219"
---
# <a name="publish-an-aspnet-core-no-locsignalr-app-to-azure-app-service"></a>Veröffentlichen einer ASP.net Core- SignalR app in Azure App Service

Von [Brady Gastern](https://twitter.com/bradygaster)

[Azure App Service](/azure/app-service/app-service-web-overview) ist ein [Microsoft Cloud Computing](https://azure.microsoft.com/) Platform-Dienst zum Hosting von Web-Apps, einschließlich ASP.net Core.

> [!NOTE]
> Dieser Artikel bezieht sich auf das Veröffentlichen einer ASP.net Core- SignalR App aus Visual Studio. Weitere Informationen finden Sie unter [ SignalR Service for Azure](https://azure.microsoft.com/services/signalr-service).

## <a name="publish-the-app"></a>Veröffentlichen der App

In diesem Artikel wird die Veröffentlichung mit den Tools in Visual Studio behandelt. Visual Studio Code Benutzer können [Azure CLI](/cli/azure) Befehle zum Veröffentlichen von apps in Azure verwenden. Weitere Informationen finden Sie unter [Veröffentlichen einer ASP.net Core-app in Azure mit Befehlszeilen Tools](/azure/app-service/app-service-web-get-started-dotnet).

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen** aus.

1. Vergewissern Sie sich, dass im Dialogfeld **Veröffentlichungsziel auswählen** die Option **App Service** und **neu erstellen** ausgewählt ist.

1. Wählen Sie im Dropdown Feld " **veröffentlichen** " die Option **Profil erstellen** aus.

   Geben Sie die in der folgenden Tabelle beschriebenen Informationen in das Dialogfeld **App Service erstellen** ein, und wählen Sie **Erstellen** aus.

   | Element               | Beschreibung |
   | ------------------ | ----------- |
   | **Name**           | Der eindeutige Name der app. |
   | **Abonnement**   | Azure-Abonnement, das von der APP verwendet wird. |
   | **Ressourcengruppe** | Gruppe verwandter Ressourcen, zu denen die APP gehört. |
   | **Hostingplan**   | Der Tarif für die Web-App. |

1. Wählen Sie in der Dropdown Liste **Abhängigkeiten** hinzufügen den **Azure- SignalR Dienst** aus  >  **Add** :

   ![Bereich Abhängigkeiten mit der Auswahl von Azure::: NO-LOC (signalr)::: Service in der Dropdown Liste hinzufügen](publish-to-azure-web-app/_static/signalr-service-dependency.png)

1. Wählen Sie im Dialogfeld **Azure- SignalR Dienst** die Option **neue Azure- SignalR Dienst Instanz erstellen** aus.

1. Geben Sie einen **Namen** , eine **Ressourcengruppe** und einen **Speicherort** an. Wechseln Sie zum Dialogfeld **Azure- SignalR Dienst** , und wählen Sie **Hinzufügen** .

Visual Studio führt die folgenden Aufgaben aus:

* Erstellt ein Veröffentlichungs Profil mit Veröffentlichungs Einstellungen.
* Erstellt eine *Azure-Web-App* mit den bereitgestellten Details.
* Veröffentlicht die app.
* Hiermit wird ein Browser gestartet, der die Web-App lädt.

Das Format der App-URL lautet `{APP SERVICE NAME}.azurewebsites.net` . Eine APP `SignalRChatApp` mit dem Namen hat z. b. eine URL von `https://signalrchatapp.azurewebsites.net` .

Wenn beim Bereitstellen einer APP, die auf eine Vorschauversion von .net Core ausgerichtet ist, ein Fehler vom Typ "http *502,2-* ungültiges Gateway" auftritt, finden Sie weitere Informationen unter Bereitstellen [ASP.net Core Vorschau Release Azure App Service](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)

## <a name="configure-the-app-in-azure-app-service"></a>Konfigurieren der app in Azure App Service

> [!NOTE]
> *Dieser Abschnitt gilt nur für apps, die den Azure-Dienst nicht verwenden SignalR .*
>
> Wenn die APP den Azure- SignalR Dienst verwendet, ist für den App Service nicht die Konfiguration der in diesem Abschnitt beschriebenen arr-Affinität (Application Request Routing) und der in diesem Abschnitt beschriebenen websockets erforderlich. Clients verbinden ihre websockets mit dem Azure- SignalR Dienst und nicht direkt mit der app.

Aktivieren Sie für apps, die ohne Azure- SignalR Dienst gehostet werden, Folgendes:

* [Arr-Affinität] ( https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity- cookie -(Arr- cookie ) -for-Azure-web-apps.html), um Anforderungen von einem Benutzer zurück an dieselbe App Service Instanz weiterzuleiten. Die Standardeinstellung ist **on** .
* [Websockets](xref:fundamentals/websockets) , damit der websockets-Transport funktioniert. Die Standardeinstellung ist **Off** .

1. Navigieren Sie in der Azure-Portal zu der Web-App in **App Services** .
1. Öffnen Sie die allgemeinen **Konfigurations**  >  **Einstellungen** .
1. Legen Sie **websockets** **auf ein** fest.
1. Vergewissern Sie sich, dass **arr-Affinität** **auf** ein festgelegt ist.

## <a name="app-service-plan-limits"></a>App Service Plan Limits

Websockets und andere Transporte sind auf Grundlage des ausgewählten App Service Plans beschränkt. Weitere Informationen finden Sie in den Abschnitten *Azure-Cloud Services Limits* und *App Service Limits* im Artikel Einschränkungen für [Azure-Abonnements und Dienste, Kontingente und Einschränkungen](/azure/azure-subscription-service-limits#app-service-limits) .

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Was ist Azure SignalR Service?](/azure/azure-signalr/signalr-overview)
* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [Veröffentlichen einer ASP.NET Core-App in Azure mithilfe von Befehlszeilentools](/azure/app-service/app-service-web-get-started-dotnet)
* [Hosten und Bereitstellen ASP.net Core Vorschau-apps in Azure](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
