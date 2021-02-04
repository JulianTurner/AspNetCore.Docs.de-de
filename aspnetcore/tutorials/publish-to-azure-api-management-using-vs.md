---
title: Veröffentlichen einer ASP.NET Core-Web-API in Azure API Management mit Visual Studio
author: codemillmatt
description: In diesem Artikel erfahren Sie, wie Sie eine ASP.NET Core-Web-API mithilfe von Visual Studio in Azure API Management veröffentlichen.
ms.author: masoucou
ms.custom: devx-track-csharp, mvc
ms.date: 11/22/2020
uid: tutorials/publish-to-azure-api-management-using-vs
ms.openlocfilehash: ddff54bbd146c98cf83a865910401df26e7ac4ec
ms.sourcegitcommit: 7e394a8527c9818caebb940f692ae4fcf2f1b277
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217582"
---
# <a name="publish-an-aspnet-core-web-api-to-azure-api-management-with-visual-studio"></a>Veröffentlichen einer ASP.NET Core-Web-API in Azure API Management mit Visual Studio

Von [Matt Soucoup](https://twitter.com/codemillmatt)

## <a name="set-up"></a>Einrichten

- Eröffnen Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/dotnet/), wenn Sie noch über kein Konto verfügen.
- [Erstellen Sie eine neue Azure API Management-Instanz](/azure/api-management/get-started-create-service-instance), sofern Sie dies noch nicht getan haben.
- [Erstellen Sie ein Web-API-App-Projekt](xref:tutorials/first-web-api#create-a-web-project).

## <a name="configure-the-app"></a>Konfigurieren der App

Durch das Hinzufügen von Swagger-Definitionen zur ASP.NET Core-Web-API wird Azure API Management das Lesen aus den API-Definitionen der App ermöglicht. Führen Sie die folgenden Schritte aus.

### <a name="add-swagger"></a>Hinzufügen von Swagger

1. Fügen Sie das NuGet-Paket [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore) zum ASP.NET Core-Web-API-Projekt hinzu:

    ![Screenshot: Konfigurieren des NuGet-Dialogfelds](publish-to-azure-api-management-using-vs/_static/configure_nuget.png)

1. Fügen Sie die folgende Zeile in die `Startup.ConfigureServices` Methode ein:
    
    ```csharp
    services.AddSwaggerGen();
    ```
    
1. Fügen Sie die folgende Zeile in die `Startup.Configure` Methode ein:

    ```csharp
    app.UseSwagger();
    ```

### <a name="change-the-api-routing"></a>Ändern des API-Routings

Als Nächstes ändern Sie die URL-Struktur, die für den Zugriff auf die `Get`-Aktion von `WeatherForecastController` erforderlich ist. Führen Sie die folgenden Schritte aus:

1. Öffnen Sie die Datei *WeatherForecastController.cs*.
1. Löschen Sie das `[Route("[controller]")]`-Attribut auf Klassenebene. Die Klassendefinition sollte wie folgt aussehen:

    ```csharp
    [ApiController]
    public class WeatherForecastController : ControllerBase
    ```

1. Fügen Sie ein `[Route("/")]`-Attribut zur `Get()`-Aktion hinzu. Die Funktionsdefinition sollte wie folgt aussehen:

    ```csharp
    [HttpGet]
    [Route("/")]
    public IEnumerable<WeatherForecast> Get()
    ```

## <a name="publish-the-web-api-to-azure-app-service"></a>Veröffentlichen der Web-API in Azure App Service

Führen Sie die folgenden Schritte aus, um die ASP.NET Core-Web-API in Azure API Management zu veröffentlichen:

1. Veröffentlichen Sie die API-App in Azure App Service.
1. Fügen Sie eine leere API zur Azure API Management-Dienstinstanz hinzu.
1. Veröffentlichen Sie die ASP.NET Core-Web-API-App in der Azure API Management-Dienstinstanz.

### <a name="publish-the-api-app-to-azure-app-service"></a>Veröffentlichen Sie die API-App in Azure App Service

Führen Sie die folgenden Schritte aus, um die ASP.NET Core-Web-API in Azure API Management zu veröffentlichen:

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen** aus:

    ![Geöffnetes Kontextmenü mit hervorgehobenem Link „Veröffentlichen“](publish-to-azure-api-management-using-vs/_static/publish_menu.png)

1. Wählen Sie im Dialogfeld **Veröffentlichen** die Option **Azure** aus, und klicken Sie dann auf **Weiter**:

    ![Dialogfeld „Veröffentlichen“](publish-to-azure-api-management-using-vs/_static/publish_dialog.png)

1. Wählen Sie **Azure App Service (Windows)** aus, und klicken Sie dann auf **Weiter**:

    ![Dialogfeld „Veröffentlichen“: App Service auswählen](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc.png)

1. Klicken Sie auf **Create a new Azure App Service** (Neue Azure App Service-Instanz erstellen).

    ![Dialogfeld „Veröffentlichen“: Azure Service-Instanz auswählen](publish-to-azure-api-management-using-vs/_static/publish_dialog_create_new_app_svc.png)

    Das Dialogfeld **App Service erstellen** wird angezeigt. Die Eingabefelder **App-Name**, **Ressourcengruppe** und **App Service-Plan** werden aufgefüllt. Sie können diese Namen beibehalten oder ändern.
1. Wählen Sie die Schaltfläche **Erstellen**.

    ![Dialogfeld „App Service erstellen“](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_attributes.png)

Nachdem die Erstellung abgeschlossen ist, wird das Dialogfeld automatisch geschlossen und das Dialogfeld **Veröffentlichen** wird wieder angezeigt. Die soeben erstellte Instanz wird automatisch ausgewählt.

![Dialogfeld „Veröffentlichen“: App Service-Instanz auswählen](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

An diesem Punkt müssen Sie eine API zum Azure API Management-Dienst hinzufügen. Schließen Sie Visual Studio nicht, während Sie die folgenden Aufgaben durchführen.

### <a name="add-an-api-to-azure-api-management"></a>Hinzufügen einer API zu Azure API Management

1. Öffnen Sie die API Management-Dienstinstanz, die Sie zuvor im Azure-Portal erstellt haben. Wählen Sie das Blatt **APIs** aus:

  ![Ausgewähltes Blatt „APIs“ in der API Management-Dienstinstanz](publish-to-azure-api-management-using-vs/_static/portal_api_overview.png)

1. Wählen Sie die drei Punkte neben **Echo-API** aus, und wählen Sie dann aus dem Popupmenü **Löschen** aus, um es zu entfernen.

  ![Löschen der Echo-API aus der API Management-Dienstinstanz](publish-to-azure-api-management-using-vs/_static/portal_delete_echo.png)

1. Klicken Sie im Bereich **Neue API hinzufügen** auf die Kachel **Leere API**:

  ![Screenshot: Hervorgehobene Kachel „Leere API“](publish-to-azure-api-management-using-vs/_static/portal_api_create_blank.png)

1. Geben Sie die folgenden Werte in das angezeigte Dialogfeld **Leere API erstellen** ein:    

    - **Anzeigename**: *WeatherForecasts*
    - **Name:** *weatherforecasts*
    - **API-URL-Suffix:** *v1*
    - Lassen Sie das Feld **Webdienst-URL** leer.

1. Wählen Sie die Schaltfläche **Erstellen**.

    ![Screenshot: Ausgefülltes Dialogfeld „Leere API erstellen“](publish-to-azure-api-management-using-vs/_static/portal_api_blank_complete.png)

Daraufhin wird die leere API erstellt.

![Screenshot: Leere API im API Management-Portal](publish-to-azure-api-management-using-vs/_static/portal_api_blank_created_empty.png)

### <a name="publish-the-aspnet-core-web-api-to-azure-api-management"></a>Veröffentlichen der ASP.NET Core-Web-API in Azure API Management

1. Wechseln Sie zurück zu Visual Studio. Das Dialogfeld **Veröffentlichen** sollte immer noch geöffnet sein.
1. Wählen Sie die neu veröffentlichte Azure App Service-Instanz aus, sodass diese hervorgehoben wird.
1. Wählen Sie die Schaltfläche **Weiter** aus.

    ![Screenshot: Dialogfeld „Veröffentlichen“ mit ausgewähltem App-Dienst](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

1. Im Dialogfeld wird nun der zuvor erstellte Azure API Management-Dienst angezeigt. Erweitern Sie diesen und den Ordner *APIs*, um die leere API anzuzeigen, die Sie erstellt haben.
1. Wählen Sie den Namen der leeren API aus, und klicken Sie dann auf **Fertigstellen**.

    ![Screenshot: Neu erstellte, leere Azure API Management-API, die im Dialogfeld „Veröffentlichen“ ausgewählt ist](publish-to-azure-api-management-using-vs/_static/publish_dialog_api_selected.png)

1. Das Dialogfeld wird geschlossen und eine Zusammenfassung mit Informationen zur Veröffentlichung wird angezeigt. Wählen Sie die Schaltfläche **Veröffentlichen** aus.

    ![Screenshot: Visual Studio mit dem angezeigten Veröffentlichungsprofil](publish-to-azure-api-management-using-vs/_static/vs_publish_profile.png)

    Die Web-API wird in Azure App Service und Azure API Management veröffentlicht. Dann wird ein neues Browserfenster angezeigt, indem die API angezeigt wird, die in Azure App Service ausgeführt wird. Sie können dieses Fenster schließen.

1. Wechseln Sie zurück zur Azure API Management-Instanz im Azure-Portal.
1. Aktualisieren Sie das Browserfenster.
1. Wählen Sie die leere API aus, die Sie in den vorherigen Schritten erstellt haben. Diese wurde nun aufgefüllt, und Sie können sie untersuchen.

    ![Screenshot: Aufgefüllte API in Azure API Management](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt.png)

### <a name="configure-the-published-api-name"></a>Konfigurieren des veröffentlichten API-Namens

Beachten Sie, dass sich der Name der API von dem Namen unterscheidet, den Sie angegeben haben. Die veröffentlichte API hat den Namen *WeatherAPI*, obwohl Sie ihr beim Erstellen den Namen *WeatherForecasts* gegeben haben. Führen Sie die folgenden Schritte aus, um den Namen zu beheben:

![Screenshot: Aufgefüllte API mit den unterschiedlichen Namen hervorgehoben](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt_wrong_name.png)

1. Löschen Sie die folgende Zeile aus der `Startup.ConfigureServices`-Methode:
    
    ```csharp
    services.AddSwaggerGen();
    ```

1. Fügen Sie der `Startup.ConfigureServices`-Methode folgenden Code hinzu:
    
    ```csharp
    services.AddSwaggerGen(config =>
    {
        config.SwaggerDoc("v1", new Microsoft.OpenApi.Models.OpenApiInfo
        {
            Title = "WeatherForecasts",
            Version = "v1"
        });
    });
    ```

1. Öffnen Sie das neu erstellte Veröffentlichungsprofil. Dieses finden Sie im **Projektmappen-Explorer** im Ordner *Properties/PublishProfiles*.

    ![Screenshot: Hervorgehobener Speicherort der Veröffentlichungsprofildatei](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_highlighted.png)

1. Ändern Sie den Wert des `<OpenAPIDocumentName>`-Elements von `v1` in `WeatherForecasts`.

    ![Screenshot: Erforderliche Änderungen am Veröffentlichungsprofil](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_changes.png)

1. Führen Sie die Veröffentlichung der ASP.NET Core-Web-API noch mal durch, und öffnen Sie die Azure API Management-Instanz im Azure-Portal.
1. Aktualisieren Sie die Seite im Browser. Daraufhin sollte der richtige Name der API angezeigt werden.

    ![Screenshot: Fertiggestellte API im Portal](publish-to-azure-api-management-using-vs/_static/portal_finish.png)

### <a name="verify-the-web-api-is-working"></a>Überprüfen der Funktionsweise der Web-API

Sie können die in Azure API Management bereitgestellte ASP.NET Core-Web-API anhand der folgenden Schritte über das Azure-Portal testen:

1. Öffnen Sie die Registerkarte **Testen**.
1. Klicken Sie auf **/** oder den **Get**-Vorgang.
1. Wählen Sie **Send** (Senden) aus.

    ![Screenshot: Portal vor dem Test](publish-to-azure-api-management-using-vs/_static/portal_pre_test.png)

Eine erfolgreiche Antwort sieht wie folgt aus:

![Screenshot: Erfolgreiche Antwort von API Management](publish-to-azure-api-management-using-vs/_static/portal_successful_response.png)

## <a name="clean-up"></a>Bereinigung

Sobald Sie das Testen der App abgeschlossen haben, können Sie zum [Azure-Portal](https://portal.azure.com/) wechseln und die App löschen.

1. Wählen Sie **Ressourcengruppen** aus, und wählen Sie dann die Ressourcengruppe aus, die Sie erstellt haben.

    ![Azure-Portal: Ressourcengruppen im Menü auf der Randleiste](publish-to-azure-api-management-using-vs/_static/portalrg.png)

1. Wählen Sie auf der Seite **Ressourcengruppen** **Löschen** aus.

    ![Azure-Portal: Seite „Ressourcengruppen“](publish-to-azure-api-management-using-vs/_static/rgd.png)

1. Geben Sie den Namen der Ressourcengruppe ein, und klicken Sie auf **Löschen**. Ihre App und alle anderen in diesem Tutorial erstellten Ressourcen werden nun aus Azure gelöscht.

## <a name="next-steps"></a>Nächste Schritte

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Azure API Management](/azure/api-management/api-management-key-concepts)
- [Azure App Service](/azure/app-service/app-service-web-overview)
