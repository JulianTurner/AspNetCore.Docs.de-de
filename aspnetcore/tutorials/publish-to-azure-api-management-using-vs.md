---
title: Veröffentlichen einer ASP.NET Core-Web-API in Azure API Management mit Visual Studio
author: codemillmatt
description: In diesem Artikel erfahren Sie, wie Sie eine ASP.NET Core-Web-API mithilfe von Visual Studio in Azure API Management veröffentlichen.
ms.author: masoucou
ms.custom: devx-track-csharp, mvc
ms.date: 11/22/2020
uid: tutorials/publish-to-azure-api-management-using-vs
ms.openlocfilehash: 395b5981a3018486235c38f032893f985ab71383
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "96332214"
---
# <a name="publish-an-aspnet-core-web-api-to-azure-api-management-with-visual-studio"></a><span data-ttu-id="5694c-103">Veröffentlichen einer ASP.NET Core-Web-API in Azure API Management mit Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5694c-103">Publish an ASP.NET Core web API to Azure API Management with Visual Studio</span></span>

<span data-ttu-id="5694c-104">Von [Matt Soucoup](https://twitter.com/codemillmatt)</span><span class="sxs-lookup"><span data-stu-id="5694c-104">By [Matt Soucoup](https://twitter.com/codemillmatt)</span></span>

## <a name="set-up"></a><span data-ttu-id="5694c-105">Einrichten</span><span class="sxs-lookup"><span data-stu-id="5694c-105">Set up</span></span>

- <span data-ttu-id="5694c-106">Eröffnen Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/dotnet/), wenn Sie noch über kein Konto verfügen.</span><span class="sxs-lookup"><span data-stu-id="5694c-106">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span>
- <span data-ttu-id="5694c-107">[Erstellen Sie eine neue Azure API Management-Instanz](/azure/api-management/get-started-create-service-instance), sofern Sie dies noch nicht getan haben.</span><span class="sxs-lookup"><span data-stu-id="5694c-107">[Create a new Azure API Management instance](/azure/api-management/get-started-create-service-instance) if you haven't already.</span></span>
- <span data-ttu-id="5694c-108">[Erstellen Sie ein Web-API-App-Projekt](xref:tutorials/first-web-api#create-a-web-project).</span><span class="sxs-lookup"><span data-stu-id="5694c-108">[Create a web API app project](xref:tutorials/first-web-api#create-a-web-project).</span></span>

## <a name="configure-the-app"></a><span data-ttu-id="5694c-109">Konfigurieren der App</span><span class="sxs-lookup"><span data-stu-id="5694c-109">Configure the app</span></span>

<span data-ttu-id="5694c-110">Durch das Hinzufügen von Swagger-Definitionen zur ASP.NET Core-Web-API wird Azure API Management das Lesen aus den API-Definitionen der App ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="5694c-110">Adding Swagger definitions to the ASP.NET Core web API allows Azure API Management to read the app's API definitions.</span></span> <span data-ttu-id="5694c-111">Führen Sie die folgenden Schritte aus.</span><span class="sxs-lookup"><span data-stu-id="5694c-111">Complete the following steps.</span></span>

### <a name="add-swagger"></a><span data-ttu-id="5694c-112">Hinzufügen von Swagger</span><span class="sxs-lookup"><span data-stu-id="5694c-112">Add Swagger</span></span>

1. <span data-ttu-id="5694c-113">Fügen Sie das NuGet-Paket [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore) zum ASP.NET Core-Web-API-Projekt hinzu:</span><span class="sxs-lookup"><span data-stu-id="5694c-113">Add the [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore) NuGet package to the ASP.NET Core web API project:</span></span>

    ![Screenshot: Konfigurieren des NuGet-Dialogfelds](publish-to-azure-api-management-using-vs/_static/configure_nuget.png)

1. <span data-ttu-id="5694c-115">Fügen Sie die folgende Zeile in die `Startup.ConfigureServices` Methode ein:</span><span class="sxs-lookup"><span data-stu-id="5694c-115">Add the following line to the `Startup.ConfigureServices` method:</span></span>
    
    ```csharp
    services.AddSwaggerGen();
    ```
    
1. <span data-ttu-id="5694c-116">Fügen Sie die folgende Zeile in die `Startup.Configure` Methode ein:</span><span class="sxs-lookup"><span data-stu-id="5694c-116">Add the following line to the `Startup.Configure` method:</span></span>

    ```csharp
    app.UseSwagger();
    ```

### <a name="change-the-api-routing"></a><span data-ttu-id="5694c-117">Ändern des API-Routings</span><span class="sxs-lookup"><span data-stu-id="5694c-117">Change the API routing</span></span>

<span data-ttu-id="5694c-118">Als Nächstes ändern Sie die URL-Struktur, die für den Zugriff auf die `Get`-Aktion von `WeatherForecastController` erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="5694c-118">Next, you'll change the URL structure needed to access the `Get` action of the `WeatherForecastController`.</span></span> <span data-ttu-id="5694c-119">Führen Sie die folgenden Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="5694c-119">Complete the following steps:</span></span>

1. <span data-ttu-id="5694c-120">Öffnen Sie die Datei *WeatherForecastController.cs*.</span><span class="sxs-lookup"><span data-stu-id="5694c-120">Open the *WeatherForecastController.cs* file.</span></span>
1. <span data-ttu-id="5694c-121">Löschen Sie das `[Route("[controller]")]`-Attribut auf Klassenebene.</span><span class="sxs-lookup"><span data-stu-id="5694c-121">Delete the `[Route("[controller]")]` class-level attribute.</span></span> <span data-ttu-id="5694c-122">Die Klassendefinition sollte wie folgt aussehen:</span><span class="sxs-lookup"><span data-stu-id="5694c-122">The class definition will look like the following:</span></span>

    ```csharp
    [ApiController]
    public class WeatherForecastController : ControllerBase
    ```

1. <span data-ttu-id="5694c-123">Fügen Sie ein `[Route("/")]`-Attribut zur `Get()`-Aktion hinzu.</span><span class="sxs-lookup"><span data-stu-id="5694c-123">Add a `[Route("/")]` attribute to the `Get()` action.</span></span> <span data-ttu-id="5694c-124">Die Funktionsdefinition sollte wie folgt aussehen:</span><span class="sxs-lookup"><span data-stu-id="5694c-124">The function definition will look like the following:</span></span>

    ```csharp
    [HttpGet]
    [Route("/")]
    public IEnumerable<WeatherForecast> Get()
    ```

## <a name="publish-the-web-api-to-azure-app-service"></a><span data-ttu-id="5694c-125">Veröffentlichen der Web-API in Azure App Service</span><span class="sxs-lookup"><span data-stu-id="5694c-125">Publish the web API to Azure App Service</span></span>

<span data-ttu-id="5694c-126">Führen Sie die folgenden Schritte aus, um die ASP.NET Core-Web-API in Azure API Management zu veröffentlichen:</span><span class="sxs-lookup"><span data-stu-id="5694c-126">Complete the following steps to publish the ASP.NET Core web API to Azure API Management:</span></span>

1. <span data-ttu-id="5694c-127">Veröffentlichen Sie die API-App in Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="5694c-127">Publish the API app to Azure App Service.</span></span>
1. <span data-ttu-id="5694c-128">Fügen Sie eine leere API zur Azure API Management-Dienstinstanz hinzu.</span><span class="sxs-lookup"><span data-stu-id="5694c-128">Add a blank API to the Azure API Management service instance.</span></span>
1. <span data-ttu-id="5694c-129">Veröffentlichen Sie die ASP.NET Core-Web-API-App in der Azure API Management-Dienstinstanz.</span><span class="sxs-lookup"><span data-stu-id="5694c-129">Publish the ASP.NET Core web API app to the Azure API Management service instance.</span></span>

### <a name="publish-the-api-app-to-azure-app-service"></a><span data-ttu-id="5694c-130">Veröffentlichen Sie die API-App in Azure App Service</span><span class="sxs-lookup"><span data-stu-id="5694c-130">Publish the API app to Azure App Service</span></span>

<span data-ttu-id="5694c-131">Führen Sie die folgenden Schritte aus, um die ASP.NET Core-Web-API in Azure API Management zu veröffentlichen:</span><span class="sxs-lookup"><span data-stu-id="5694c-131">Complete the following steps to publish the ASP.NET Core web API to Azure API Management:</span></span>

1. <span data-ttu-id="5694c-132">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen** aus:</span><span class="sxs-lookup"><span data-stu-id="5694c-132">In **Solution Explorer**, right-click the project and select **Publish**:</span></span>

    ![Geöffnetes Kontextmenü mit hervorgehobenem Link „Veröffentlichen“](publish-to-azure-api-management-using-vs/_static/publish_menu.png)

1. <span data-ttu-id="5694c-134">Wählen Sie im Dialogfeld **Veröffentlichen** die Option **Azure** aus, und klicken Sie dann auf **Weiter**:</span><span class="sxs-lookup"><span data-stu-id="5694c-134">In the **Publish** dialog, select **Azure** and select the **Next** button:</span></span>

    ![Dialogfeld „Veröffentlichen“](publish-to-azure-api-management-using-vs/_static/publish_dialog.png)

1. <span data-ttu-id="5694c-136">Wählen Sie **Azure App Service (Windows)** aus, und klicken Sie dann auf **Weiter**:</span><span class="sxs-lookup"><span data-stu-id="5694c-136">Select **Azure App Service (Windows)** and select the **Next** button:</span></span>

    ![Dialogfeld „Veröffentlichen“: App Service auswählen](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc.png)

1. <span data-ttu-id="5694c-138">Klicken Sie auf **Create a new Azure App Service** (Neue Azure App Service-Instanz erstellen).</span><span class="sxs-lookup"><span data-stu-id="5694c-138">Select **Create a new Azure App Service**.</span></span>

    ![Dialogfeld „Veröffentlichen“: Azure Service-Instanz auswählen](publish-to-azure-api-management-using-vs/_static/publish_dialog_create_new_app_svc.png)

    <span data-ttu-id="5694c-140">Das Dialogfeld **App Service erstellen** wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="5694c-140">The **Create App Service** dialog appears.</span></span> <span data-ttu-id="5694c-141">Die Eingabefelder **App-Name**, **Ressourcengruppe** und **App Service-Plan** werden aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="5694c-141">The **App Name**, **Resource Group**, and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="5694c-142">Sie können diese Namen beibehalten oder ändern.</span><span class="sxs-lookup"><span data-stu-id="5694c-142">You can keep these names or change them.</span></span>
1. <span data-ttu-id="5694c-143">Wählen Sie die Schaltfläche **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="5694c-143">Select the **Create** button.</span></span>

    ![Dialogfeld „App Service erstellen“](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_attributes.png)

<span data-ttu-id="5694c-145">Nachdem die Erstellung abgeschlossen ist, wird das Dialogfeld automatisch geschlossen und das Dialogfeld **Veröffentlichen** wird wieder angezeigt.</span><span class="sxs-lookup"><span data-stu-id="5694c-145">After creation is completed, the dialog is automatically closed and the **Publish** dialog gets focus again.</span></span> <span data-ttu-id="5694c-146">Die soeben erstellte Instanz wird automatisch ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="5694c-146">The instance that was created is automatically selected.</span></span>

![Dialogfeld „Veröffentlichen“: App Service-Instanz auswählen](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

<span data-ttu-id="5694c-148">An diesem Punkt müssen Sie eine API zum Azure API Management-Dienst hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="5694c-148">At this point, you need to add an API to the Azure API Management service.</span></span> <span data-ttu-id="5694c-149">Schließen Sie Visual Studio nicht, während Sie die folgenden Aufgaben durchführen.</span><span class="sxs-lookup"><span data-stu-id="5694c-149">Leave Visual Studio open while you complete the following tasks.</span></span>

### <a name="add-an-api-to-azure-api-management"></a><span data-ttu-id="5694c-150">Hinzufügen einer API zu Azure API Management</span><span class="sxs-lookup"><span data-stu-id="5694c-150">Add an API to Azure API Management</span></span>

1. <span data-ttu-id="5694c-151">Öffnen Sie die API Management-Dienstinstanz, die Sie zuvor im Azure-Portal erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="5694c-151">Open the API Management Service instance created previously in the Azure portal.</span></span> <span data-ttu-id="5694c-152">Wählen Sie das Blatt **APIs** aus:</span><span class="sxs-lookup"><span data-stu-id="5694c-152">Select the **APIs** blade:</span></span>

  ![Ausgewähltes Blatt „APIs“ in der API Management-Dienstinstanz](publish-to-azure-api-management-using-vs/_static/portal_api_overview.png)

1. <span data-ttu-id="5694c-154">Wählen Sie die drei Punkte neben **Echo-API** aus, und wählen Sie dann aus dem Popupmenü **Löschen** aus, um es zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="5694c-154">Select the 3 dots next to **Echo API** and then select **Delete** from the pop-up menu to remove it.</span></span>

  ![Löschen der Echo-API aus der API Management-Dienstinstanz](publish-to-azure-api-management-using-vs/_static/portal_delete_echo.png)

1. <span data-ttu-id="5694c-156">Klicken Sie im Bereich **Neue API hinzufügen** auf die Kachel **Leere API**:</span><span class="sxs-lookup"><span data-stu-id="5694c-156">From the **Add a new API** panel, select the **Blank API** tile:</span></span>

  ![Screenshot: Hervorgehobene Kachel „Leere API“](publish-to-azure-api-management-using-vs/_static/portal_api_create_blank.png)

1. <span data-ttu-id="5694c-158">Geben Sie die folgenden Werte in das angezeigte Dialogfeld **Leere API erstellen** ein:</span><span class="sxs-lookup"><span data-stu-id="5694c-158">Enter the following values in the **Create a blank API** dialog that appears:</span></span>    

    - <span data-ttu-id="5694c-159">**Anzeigename**: *WeatherForecasts*</span><span class="sxs-lookup"><span data-stu-id="5694c-159">**Display Name**: *WeatherForecasts*</span></span>
    - <span data-ttu-id="5694c-160">**Name:** *weatherforecasts*</span><span class="sxs-lookup"><span data-stu-id="5694c-160">**Name**: *weatherforecasts*</span></span>
    - <span data-ttu-id="5694c-161">**API-URL-Suffix:** *v1*</span><span class="sxs-lookup"><span data-stu-id="5694c-161">**API Url suffix**: *v1*</span></span>
    - <span data-ttu-id="5694c-162">Lassen Sie das Feld **Webdienst-URL** leer.</span><span class="sxs-lookup"><span data-stu-id="5694c-162">Leave the **Web service URL** field empty.</span></span>

1. <span data-ttu-id="5694c-163">Wählen Sie die Schaltfläche **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="5694c-163">Select the **Create** button.</span></span>

    ![Screenshot: Ausgefülltes Dialogfeld „Leere API erstellen“](publish-to-azure-api-management-using-vs/_static/portal_api_blank_complete.png)

<span data-ttu-id="5694c-165">Daraufhin wird die leere API erstellt.</span><span class="sxs-lookup"><span data-stu-id="5694c-165">The blank API is created.</span></span>

![Screenshot: Leere API im API Management-Portal](publish-to-azure-api-management-using-vs/_static/portal_api_blank_created_empty.png)

### <a name="publish-the-aspnet-core-web-api-to-azure-api-management"></a><span data-ttu-id="5694c-167">Veröffentlichen der ASP.NET Core-Web-API in Azure API Management</span><span class="sxs-lookup"><span data-stu-id="5694c-167">Publish the ASP.NET Core web API to Azure API Management</span></span>

1. <span data-ttu-id="5694c-168">Wechseln Sie zurück zu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5694c-168">Switch back to Visual Studio.</span></span> <span data-ttu-id="5694c-169">Das Dialogfeld **Veröffentlichen** sollte immer noch geöffnet sein.</span><span class="sxs-lookup"><span data-stu-id="5694c-169">The **Publish** dialog should still be open where you left off before.</span></span>
1. <span data-ttu-id="5694c-170">Wählen Sie die neu veröffentlichte Azure App Service-Instanz aus, sodass diese hervorgehoben wird.</span><span class="sxs-lookup"><span data-stu-id="5694c-170">Select the newly published Azure App Service so it's highlighted.</span></span>
1. <span data-ttu-id="5694c-171">Wählen Sie die Schaltfläche **Weiter** aus.</span><span class="sxs-lookup"><span data-stu-id="5694c-171">Select the **Next** button.</span></span>

    ![Screenshot: Dialogfeld „Veröffentlichen“ mit ausgewähltem App-Dienst](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

1. <span data-ttu-id="5694c-173">Im Dialogfeld wird nun der zuvor erstellte Azure API Management-Dienst angezeigt.</span><span class="sxs-lookup"><span data-stu-id="5694c-173">The dialog now shows the Azure API Management service created before.</span></span> <span data-ttu-id="5694c-174">Erweitern Sie diesen und den Ordner *APIs*, um die leere API anzuzeigen, die Sie erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="5694c-174">Expand it and the *APIs* folder to see the blank API you created.</span></span>
1. <span data-ttu-id="5694c-175">Wählen Sie den Namen der leeren API aus, und klicken Sie dann auf **Fertigstellen**.</span><span class="sxs-lookup"><span data-stu-id="5694c-175">Select the blank API's name and select the **Finish** button.</span></span>

    ![Screenshot: Neu erstellte, leere Azure API Management-API, die im Dialogfeld „Veröffentlichen“ ausgewählt ist](publish-to-azure-api-management-using-vs/_static/publish_dialog_api_selected.png)

1. <span data-ttu-id="5694c-177">Das Dialogfeld wird geschlossen und eine Zusammenfassung mit Informationen zur Veröffentlichung wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="5694c-177">The dialog closes and a summary screen appears with information about the publish.</span></span> <span data-ttu-id="5694c-178">Wählen Sie die Schaltfläche **Veröffentlichen** aus.</span><span class="sxs-lookup"><span data-stu-id="5694c-178">Select the **Publish** button.</span></span>

    ![Screenshot: Visual Studio mit dem angezeigten Veröffentlichungsprofil](publish-to-azure-api-management-using-vs/_static/vs_publish_profile.png)

    <span data-ttu-id="5694c-180">Die Web-API wird in Azure App Service und Azure API Management veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="5694c-180">The web API will publish to both Azure App Service and Azure API Management.</span></span> <span data-ttu-id="5694c-181">Dann wird ein neues Browserfenster angezeigt, indem die API angezeigt wird, die in Azure App Service ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="5694c-181">A new browser window will appear and show the API running in Azure App Service.</span></span> <span data-ttu-id="5694c-182">Sie können dieses Fenster schließen.</span><span class="sxs-lookup"><span data-stu-id="5694c-182">You can close that window.</span></span>

1. <span data-ttu-id="5694c-183">Wechseln Sie zurück zur Azure API Management-Instanz im Azure-Portal.</span><span class="sxs-lookup"><span data-stu-id="5694c-183">Switch back to the Azure API Management instance in the Azure portal.</span></span>
1. <span data-ttu-id="5694c-184">Aktualisieren Sie das Browserfenster.</span><span class="sxs-lookup"><span data-stu-id="5694c-184">Refresh the browser window.</span></span>
1. <span data-ttu-id="5694c-185">Wählen Sie die leere API aus, die Sie in den vorherigen Schritten erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="5694c-185">Select the blank API you created in the preceding steps.</span></span> <span data-ttu-id="5694c-186">Diese wurde nun aufgefüllt, und Sie können sie untersuchen.</span><span class="sxs-lookup"><span data-stu-id="5694c-186">It's now populated and you can explore around.</span></span>

    ![Screenshot: Aufgefüllte API in Azure API Management](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt.png)

### <a name="configure-the-published-api-name"></a><span data-ttu-id="5694c-188">Konfigurieren des veröffentlichten API-Namens</span><span class="sxs-lookup"><span data-stu-id="5694c-188">Configure the published API name</span></span>

<span data-ttu-id="5694c-189">Beachten Sie, dass sich der Name der API von dem Namen unterscheidet, den Sie angegeben haben.</span><span class="sxs-lookup"><span data-stu-id="5694c-189">Notice the name of the API is different than what you named it.</span></span> <span data-ttu-id="5694c-190">Die veröffentlichte API hat den Namen *WeatherAPI*, obwohl Sie ihr beim Erstellen den Namen *WeatherForecasts* gegeben haben.</span><span class="sxs-lookup"><span data-stu-id="5694c-190">The published API is named *WeatherAPI*; however, you named it *WeatherForecasts* when you created it.</span></span> <span data-ttu-id="5694c-191">Führen Sie die folgenden Schritte aus, um den Namen zu beheben:</span><span class="sxs-lookup"><span data-stu-id="5694c-191">Complete the following steps to fix the name:</span></span>

![Screenshot: Aufgefüllte API mit den unterschiedlichen Namen hervorgehoben](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt_wrong_name.png)

1. <span data-ttu-id="5694c-193">Löschen Sie die folgende Zeile aus der `Startup.ConfigureServices`-Methode:</span><span class="sxs-lookup"><span data-stu-id="5694c-193">Delete the following line from the `Startup.ConfigureServices` method:</span></span>
    
    ```csharp
    services.AddSwaggerGen();
    ```

1. <span data-ttu-id="5694c-194">Fügen Sie der `Startup.ConfigureServices`-Methode folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="5694c-194">Add the following code to the `Startup.ConfigureServices` method:</span></span>
    
    ```csharp
    services.AddSwaggerGen(config =>
    {
        config.SwaggerDoc("WeatherForecasts", new Microsoft.OpenApi.Models.OpenApiInfo
        {
            Title = "Weather Forecasts",
            Version = "v1"
        });
    });
    ```

1. <span data-ttu-id="5694c-195">Öffnen Sie das neu erstellte Veröffentlichungsprofil.</span><span class="sxs-lookup"><span data-stu-id="5694c-195">Open the newly created publish profile.</span></span> <span data-ttu-id="5694c-196">Dieses finden Sie im **Projektmappen-Explorer** im Ordner *Properties/PublishProfiles*.</span><span class="sxs-lookup"><span data-stu-id="5694c-196">It can be found from **Solution Explorer** in the *Properties/PublishProfiles* folder.</span></span>

    ![Screenshot: Hervorgehobener Speicherort der Veröffentlichungsprofildatei](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_highlighted.png)

1. <span data-ttu-id="5694c-198">Ändern Sie den Wert des `<OpenAPIDocumentName>`-Elements von `v1` in `WeatherForecasts`.</span><span class="sxs-lookup"><span data-stu-id="5694c-198">Change the `<OpenAPIDocumentName>` element's value from `v1` to `WeatherForecasts`.</span></span>

    ![Screenshot: Erforderliche Änderungen am Veröffentlichungsprofil](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_changes.png)

1. <span data-ttu-id="5694c-200">Führen Sie die Veröffentlichung der ASP.NET Core-Web-API noch mal durch, und öffnen Sie die Azure API Management-Instanz im Azure-Portal.</span><span class="sxs-lookup"><span data-stu-id="5694c-200">Republish the ASP.NET Core web API and open the Azure API Management instance in the Azure portal.</span></span>
1. <span data-ttu-id="5694c-201">Aktualisieren Sie die Seite im Browser.</span><span class="sxs-lookup"><span data-stu-id="5694c-201">Refresh the page in your browser.</span></span> <span data-ttu-id="5694c-202">Daraufhin sollte der richtige Name der API angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="5694c-202">You'll see the name of the API is now correct.</span></span>

    ![Screenshot: Fertiggestellte API im Portal](publish-to-azure-api-management-using-vs/_static/portal_finish.png)

### <a name="verify-the-web-api-is-working"></a><span data-ttu-id="5694c-204">Überprüfen der Funktionsweise der Web-API</span><span class="sxs-lookup"><span data-stu-id="5694c-204">Verify the web API is working</span></span>

<span data-ttu-id="5694c-205">Sie können die in Azure API Management bereitgestellte ASP.NET Core-Web-API anhand der folgenden Schritte über das Azure-Portal testen:</span><span class="sxs-lookup"><span data-stu-id="5694c-205">You can test the deployed ASP.NET Core web API in Azure API Management from the Azure portal with the following steps:</span></span>

1. <span data-ttu-id="5694c-206">Öffnen Sie die Registerkarte **Testen**.</span><span class="sxs-lookup"><span data-stu-id="5694c-206">Open the **Test** tab.</span></span>
1. <span data-ttu-id="5694c-207">Klicken Sie auf **/** oder den **Get**-Vorgang.</span><span class="sxs-lookup"><span data-stu-id="5694c-207">Select **/** or the **Get** operation.</span></span>
1. <span data-ttu-id="5694c-208">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="5694c-208">Select **Send**.</span></span>

    ![Screenshot: Portal vor dem Test](publish-to-azure-api-management-using-vs/_static/portal_pre_test.png)

<span data-ttu-id="5694c-210">Eine erfolgreiche Antwort sieht wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="5694c-210">A successful response will look like the following:</span></span>

![Screenshot: Erfolgreiche Antwort von API Management](publish-to-azure-api-management-using-vs/_static/portal_successful_response.png)

## <a name="clean-up"></a><span data-ttu-id="5694c-212">Bereinigung</span><span class="sxs-lookup"><span data-stu-id="5694c-212">Clean up</span></span>

<span data-ttu-id="5694c-213">Sobald Sie das Testen der App abgeschlossen haben, können Sie zum [Azure-Portal](https://portal.azure.com/) wechseln und die App löschen.</span><span class="sxs-lookup"><span data-stu-id="5694c-213">When you've finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

1. <span data-ttu-id="5694c-214">Wählen Sie **Ressourcengruppen** aus, und wählen Sie dann die Ressourcengruppe aus, die Sie erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="5694c-214">Select **Resource groups**, then select the resource group you created.</span></span>

    ![Azure-Portal: Ressourcengruppen im Menü auf der Randleiste](publish-to-azure-api-management-using-vs/_static/portalrg.png)

1. <span data-ttu-id="5694c-216">Wählen Sie auf der Seite **Ressourcengruppen** **Löschen** aus.</span><span class="sxs-lookup"><span data-stu-id="5694c-216">In the **Resource groups** page, select **Delete**.</span></span>

    ![Azure-Portal: Seite „Ressourcengruppen“](publish-to-azure-api-management-using-vs/_static/rgd.png)

1. <span data-ttu-id="5694c-218">Geben Sie den Namen der Ressourcengruppe ein, und klicken Sie auf **Löschen**.</span><span class="sxs-lookup"><span data-stu-id="5694c-218">Enter the name of the resource group and select **Delete**.</span></span> <span data-ttu-id="5694c-219">Ihre App und alle anderen in diesem Tutorial erstellten Ressourcen werden nun aus Azure gelöscht.</span><span class="sxs-lookup"><span data-stu-id="5694c-219">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

## <a name="next-steps"></a><span data-ttu-id="5694c-220">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="5694c-220">Next steps</span></span>

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a><span data-ttu-id="5694c-221">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="5694c-221">Additional resources</span></span>

- [<span data-ttu-id="5694c-222">Azure API Management</span><span class="sxs-lookup"><span data-stu-id="5694c-222">Azure API Management</span></span>](/azure/api-management/api-management-key-concepts)
- [<span data-ttu-id="5694c-223">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="5694c-223">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
