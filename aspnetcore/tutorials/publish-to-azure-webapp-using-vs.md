---
title: Veröffentlichen einer ASP.NET Core-App in Azure mit Visual Studio
author: rick-anderson
description: Erfahren Sie, wie eine ASP.NET Core-App in Azure App Service mit Visual Studio veröffentlicht wird.
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 07/10/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: 817169503a80a771354e32123d65ba2bf388aa2d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060221"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a><span data-ttu-id="b0aa4-103">Veröffentlichen einer ASP.NET Core-App in Azure mit Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b0aa4-103">Publish an ASP.NET Core app to Azure with Visual Studio</span></span>

<span data-ttu-id="b0aa4-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b0aa4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


<span data-ttu-id="b0aa4-105">Lesen Sie [Veröffentlichen einer Web-App in Azure App Service mit Visual Studio für Mac](/visualstudio/mac/publish-app-svc?view=vsmac-2019), wenn Sie unter macOS arbeiten.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-105">See [Publish a Web app to Azure App Service using Visual Studio for Mac](/visualstudio/mac/publish-app-svc?view=vsmac-2019) if you are working on macOS.</span></span>

<span data-ttu-id="b0aa4-106">Informationen zur Problembehandlung bei der App Service-Bereitstellung finden Sie unter <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-106">To troubleshoot an App Service deployment issue, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="set-up"></a><span data-ttu-id="b0aa4-107">Einrichten</span><span class="sxs-lookup"><span data-stu-id="b0aa4-107">Set up</span></span>

* <span data-ttu-id="b0aa4-108">Eröffnen Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/dotnet/), wenn Sie noch über kein Konto verfügen.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-108">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span> 

## <a name="create-a-web-app"></a><span data-ttu-id="b0aa4-109">Erstellen einer Web-App</span><span class="sxs-lookup"><span data-stu-id="b0aa4-109">Create a web app</span></span>

<span data-ttu-id="b0aa4-110">Klicken Sie auf der Startseite von Visual Studio auf **Datei > Neu > Projekt...** .</span><span class="sxs-lookup"><span data-stu-id="b0aa4-110">In the Visual Studio Start Page, select **File > New > Project...**</span></span>

![Datei (Menü)](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

<span data-ttu-id="b0aa4-112">Schließen Sie das Dialogfeld **Neues Projekt** ab:</span><span class="sxs-lookup"><span data-stu-id="b0aa4-112">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="b0aa4-113">Wählen Sie **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-113">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="b0aa4-114">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-114">Select **Next**.</span></span>

![Dialogfeld "Neues Projekt"](publish-to-azure-webapp-using-vs/_static/new_prj.png)

<span data-ttu-id="b0aa4-116">Gehen Sie im Dialogfeld **ASP.NET Core-Webanwendung** folgendermaßen vor:</span><span class="sxs-lookup"><span data-stu-id="b0aa4-116">In the **New ASP.NET Core Web Application** dialog:</span></span>

* <span data-ttu-id="b0aa4-117">Wählen Sie **Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-117">Select **Web Application**.</span></span>
* <span data-ttu-id="b0aa4-118">Wählen Sie **Ändern** unter „Authentifizierung“ aus.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-118">Select **Change** under Authentication.</span></span>

![Neues ASP.NET Core Web-Dialogfeld](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

<span data-ttu-id="b0aa4-120">Das Dialogfeld **Authentifizierung ändern** wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-120">The **Change Authentication** dialog appears.</span></span> 

* <span data-ttu-id="b0aa4-121">Wählen Sie **Einzelne Benutzerkonten** aus.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-121">Select **Individual User Accounts**.</span></span>
* <span data-ttu-id="b0aa4-122">Wählen Sie **OK** aus, um zur Seite **Neue ASP.NET Core-Webanwendung** zurückzukehren, und dann **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-122">Select **OK** to return to the **New ASP.NET Core Web Application** , then select **Create**.</span></span>

![Dialogfeld „New ASP.NET Core Web Authentication“ (Neue ASP.NET Core-Webauthentifizierung)](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

<span data-ttu-id="b0aa4-124">Visual Studio erstellt die Projektmappe.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-124">Visual Studio creates the solution.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="b0aa4-125">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="b0aa4-125">Run the app</span></span>

* <span data-ttu-id="b0aa4-126">Drücken Sie STRG+F5, um das Projekt auszuführen.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-126">Press CTRL+F5 to run the project.</span></span>
* <span data-ttu-id="b0aa4-127">Testen Sie den Link **Datenschutz**.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-127">Test the **Privacy** link.</span></span>

![Auf „localhost“ in Microsoft Edge geöffnete Webanwendung](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a><span data-ttu-id="b0aa4-129">Registrieren eines Benutzers</span><span class="sxs-lookup"><span data-stu-id="b0aa4-129">Register a user</span></span>

* <span data-ttu-id="b0aa4-130">Wählen Sie **Registrieren** , und registrieren Sie einen neuen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-130">Select **Register** and register a new user.</span></span> <span data-ttu-id="b0aa4-131">Sie können eine fiktive E-Mail-Adresse verwenden.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-131">You can use a fictitious email address.</span></span> <span data-ttu-id="b0aa4-132">Sobald Sie diese übermitteln, zeigt die Seite die folgende Fehlermeldung an:</span><span class="sxs-lookup"><span data-stu-id="b0aa4-132">When you submit, the page displays the following error:</span></span>

    <span data-ttu-id="b0aa4-133">*„Fehler bei einem Datenbankvorgang beim Verarbeiten der Anforderung. Das Anwenden vorhandener Migrationen für den Datenbankkontext der Anwendung kann dieses Problem beheben.“*</span><span class="sxs-lookup"><span data-stu-id="b0aa4-133">*"A database operation failed while processing the request. Applying existing migrations for Application DB context may resolve this issue."*</span></span>
* <span data-ttu-id="b0aa4-134">Wählen Sie **Migrationen anwenden** aus, und aktualisieren Sie die Seite.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-134">Select **Apply Migrations** and, once the page updates, refresh the page.</span></span>

![Fehler bei einem Datenbankvorgang beim Verarbeiten der Anforderung.](publish-to-azure-webapp-using-vs/_static/mig.png)

<span data-ttu-id="b0aa4-137">Die App zeigt die zum Registrieren des neuen Benutzers verwendete E-Mail-Adresse und den Link **Abmelden** an.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-137">The app displays the email used to register the new user and a **Logout** link.</span></span>

![In Microsoft Edge geöffnete Webanwendung](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a><span data-ttu-id="b0aa4-140">Bereitstellen der App in Azure</span><span class="sxs-lookup"><span data-stu-id="b0aa4-140">Deploy the app to Azure</span></span>

<span data-ttu-id="b0aa4-141">Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen...** aus.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-141">Right-click on the project in Solution Explorer and select **Publish...**.</span></span>

![Geöffnetes Kontextmenü mit hervorgehobenem Link „Veröffentlichen“](publish-to-azure-webapp-using-vs/_static/pub.png)

<span data-ttu-id="b0aa4-143">Führen Sie im Dialogfeld **Veröffentlichen** folgende Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="b0aa4-143">In the **Publish** dialog:</span></span>

* <span data-ttu-id="b0aa4-144">Wählen Sie **Azure** aus.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-144">Select **Azure**.</span></span>
* <span data-ttu-id="b0aa4-145">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-145">Select **Next**.</span></span>

![Dialogfeld „Veröffentlichen“](publish-to-azure-webapp-using-vs/_static/maas1.png)

<span data-ttu-id="b0aa4-147">Führen Sie im Dialogfeld **Veröffentlichen** folgende Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="b0aa4-147">In the **Publish** dialog:</span></span>

* <span data-ttu-id="b0aa4-148">Wählen Sie **Azure App Service (Linux)** aus.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-148">Select **Azure App Service (Linux)**.</span></span>
* <span data-ttu-id="b0aa4-149">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-149">Select **Next**.</span></span>

![Dialogfeld „Veröffentlichen“: Azure Service auswählen](publish-to-azure-webapp-using-vs/_static/maas2.png)

<span data-ttu-id="b0aa4-151">Wählen Sie im Dialogfeld **Veröffentlichen** die Option **Neuen Azure App Service erstellen...** aus.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-151">In the **Publish** dialog select **Create a new Azure App Service...**</span></span>

![Dialogfeld „Veröffentlichen“: Azure Service-Instanz auswählen](publish-to-azure-webapp-using-vs/_static/maas3.png)

<span data-ttu-id="b0aa4-153">Das Dialogfeld **App Service erstellen** wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="b0aa4-153">The **Create App Service** dialog appears:</span></span>

* <span data-ttu-id="b0aa4-154">Die Eingabefelder **App-Name** , **Ressourcengruppe** und **App Service-Plan** werden aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-154">The **App Name** , **Resource Group** , and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="b0aa4-155">Sie können diese Namen beibehalten oder ändern.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-155">You can keep these names or change them.</span></span>
* <span data-ttu-id="b0aa4-156">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-156">Select **Create**.</span></span>

![Dialogfeld „App Service erstellen“](publish-to-azure-webapp-using-vs/_static/newrg1.png)

<span data-ttu-id="b0aa4-158">Nachdem die Erstellung abgeschlossen ist, wird das Dialogfeld automatisch geschlossen, und das Dialogfeld **Veröffentlichen** wird wieder angezeigt:</span><span class="sxs-lookup"><span data-stu-id="b0aa4-158">After creation is completed the dialog is automatically closed and the **Publish** dialog gets focus again:</span></span>

* <span data-ttu-id="b0aa4-159">Die gerade erstellte neue Instanz wird automatisch ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-159">The new instance that was just created is automatically selected.</span></span>
* <span data-ttu-id="b0aa4-160">Wählen Sie **Fertig stellen** aus.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-160">Select **Finish**.</span></span>

![Dialogfeld „Veröffentlichen“: App Service-Instanz auswählen](publish-to-azure-webapp-using-vs/_static/select_as.png)

<span data-ttu-id="b0aa4-162">Als Nächstes wird die Seite **Zusammenfassung des Veröffentlichungsprofils** angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-162">Next you see the **Publish Profile summary** page.</span></span> <span data-ttu-id="b0aa4-163">Visual Studio hat festgestellt, dass für diese Anwendung eine SQL Server-Datenbankinstanz erforderlich ist, und Sie werden aufgefordert, Sie zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-163">Visual Studio has detected that this application requires a SQL Server database and it's asking you to configure it.</span></span> <span data-ttu-id="b0aa4-164">Wählen Sie **Konfigurieren** aus.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-164">Select **Configure**.</span></span>

![Seite „Zusammenfassung des Veröffentlichungsprofils“: Konfigurieren der SQL Server-Abhängigkeit](publish-to-azure-webapp-using-vs/_static/sql.png)

<span data-ttu-id="b0aa4-166">Das Dialogfeld **Abhängigkeit konfigurieren** wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="b0aa4-166">The **Configure dependency** dialog appears:</span></span>

* <span data-ttu-id="b0aa4-167">Wählen Sie **Azure SQL-Datenbank** aus.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-167">Select **Azure SQL Database**.</span></span>
* <span data-ttu-id="b0aa4-168">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-168">Select **Next**.</span></span>

![Dialogfeld „SQL Server-Abhängigkeit konfigurieren“](publish-to-azure-webapp-using-vs/_static/sql1.png)

<span data-ttu-id="b0aa4-170">Wählen Sie im Dialogfeld **Azure SQL-Datenbank konfigurieren** die Option **Erstellen einer SQL-Datenbank** aus.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-170">In the **Configure Azure SQL database** dialog select **Create a SQL Database**</span></span>

![Dialogfeld „Azure SQL-Datenbank konfigurieren“](publish-to-azure-webapp-using-vs/_static/sql2.png)

<span data-ttu-id="b0aa4-172">Das Dialogfeld **Erstellen einer Azure SQL-Datenbank** wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="b0aa4-172">The **Create Azure SQL Database** appears:</span></span>

* <span data-ttu-id="b0aa4-173">Die Eingabefelder **Datenbankname** , **Ressourcengruppe** , **Datenbankserver** und **App Service-Plan** werden aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-173">The **Database name** , **Resource Group** , **Database server** and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="b0aa4-174">Sie können diese Werte beibehalten oder ändern.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-174">You can keep these values or change them.</span></span>
* <span data-ttu-id="b0aa4-175">Geben Sie **Datenbankadministrator-Benutzername** und **Datenbankadministrator-Kennwort** für den ausgewählten **Datenbankserver** ein (beachten Sie, dass das verwendete Konto über die erforderlichen Berechtigungen zum Erstellen der neuen Instanz von Azure SQL-Datenbank verfügen muss).</span><span class="sxs-lookup"><span data-stu-id="b0aa4-175">Enter the **Database administrator username** and **Database administrator password** for the selected **Database server** (note the account you use must have the necessary permissions to create the new Azure SQL database)</span></span>
* <span data-ttu-id="b0aa4-176">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-176">Select **Create**.</span></span>

![Dialogfeld „Neue Azure SQL-Datenbank“](publish-to-azure-webapp-using-vs/_static/sql_create.png)

<span data-ttu-id="b0aa4-178">Nachdem die Erstellung abgeschlossen ist, wird das Dialogfeld automatisch geschlossen, und das Dialogfeld **Azure SQL-Datenbank konfigurieren** wird wieder angezeigt:</span><span class="sxs-lookup"><span data-stu-id="b0aa4-178">After creation is completed the dialog is automatically closed and the **Configure Azure SQL Database** dialog gets focus again:</span></span>

* <span data-ttu-id="b0aa4-179">Die gerade erstellte neue Instanz wird automatisch ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-179">The new instance that was just created is automatically selected.</span></span>
* <span data-ttu-id="b0aa4-180">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-180">Select **Next**.</span></span>

![Dialogfeld „Azure SQL-Datenbank konfigurieren“](publish-to-azure-webapp-using-vs/_static/sql_select.png)

<span data-ttu-id="b0aa4-182">Im nächsten Schritt des Dialogfelds **Azure SQL-Datenbank konfigurieren** :</span><span class="sxs-lookup"><span data-stu-id="b0aa4-182">In the next step of the **Configure Azure SQL Database** dialog:</span></span>

* <span data-ttu-id="b0aa4-183">Füllen Sie die Felder **Datenbankverbindungs-Benutzername** und **Datenbankverbindungs-Kennwort** aus.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-183">Enter the **Database connection user name** and **Database connection password** fields.</span></span> <span data-ttu-id="b0aa4-184">Diese Details verwendet Ihre Anwendung zur Runtime zum Herstellen einer Verbindung mit der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-184">These are the details your application will use to connect to the database at runtime.</span></span> <span data-ttu-id="b0aa4-185">Sie sollten vermeiden, den gleichen Administratorbenutzernamen und das gleiche Kennwort wie im vorherigen Schritt zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-185">Best practice is to avoid using the same details as the admin username & password used in the previous step.</span></span>
* <span data-ttu-id="b0aa4-186">Wählen Sie **Fertig stellen** aus.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-186">Select **Finish**.</span></span>

![Dialogfeld „Azure SQL-Datenbank konfigurieren“, Verbindungszeichenfolgen-Details](publish-to-azure-webapp-using-vs/_static/sql_connection.png)

<span data-ttu-id="b0aa4-188">Wählen Sie auf der Seite **Zusammenfassung des Veröffentlichungsprofils** die Option **Einstellungen** aus:</span><span class="sxs-lookup"><span data-stu-id="b0aa4-188">In the **Publish Profile summary** page select **Settings** :</span></span>

![Seite „Zusammenfassung des Veröffentlichungsprofils“: Einstellungen bearbeiten](publish-to-azure-webapp-using-vs/_static/pp_configured.png)

<span data-ttu-id="b0aa4-190">Gehen Sie im Bereich **Einstellungen** im Dialogfeld **Veröffentlichen** so vor:</span><span class="sxs-lookup"><span data-stu-id="b0aa4-190">On the **Settings** page of the **Publish** dialog:</span></span>

* <span data-ttu-id="b0aa4-191">Erweitern Sie **Datenbanken** , und aktivieren Sie **Diese Verbindungszeichenfolge zur Laufzeit verwenden**.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-191">Expand **Databases** and check **Use this connection string at runtime**.</span></span>
* <span data-ttu-id="b0aa4-192">Erweitern Sie **Entity Framework-Migrationen** , und aktivieren Sie **Diese Migration auf Veröffentlichung anwenden**.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-192">Expand **Entity Framework Migrations** and check **Apply this migration on publish**.</span></span>

* <span data-ttu-id="b0aa4-193">Klicken Sie auf **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-193">Select **Save**.</span></span> <span data-ttu-id="b0aa4-194">Visual Studio kehrt zum Dialogfeld **Veröffentlichen** zurück.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-194">Visual Studio returns to the **Publish** dialog.</span></span> 

![Dialogfeld „Veröffentlichen“: Panel „Einstellungen“](publish-to-azure-webapp-using-vs/_static/pp_settings.png)

<span data-ttu-id="b0aa4-196">Klicken Sie auf **Veröffentlichen**.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-196">Click **Publish**.</span></span> <span data-ttu-id="b0aa4-197">Visual Studio veröffentlicht Ihre App in Azure.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-197">Visual Studio publishes your app to Azure.</span></span> <span data-ttu-id="b0aa4-198">Wenn die Bereitstellung abgeschlossen ist, wird die App im Browser geöffnet.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-198">When the deployment completes, the app is opened in a browser.</span></span>

![Dialogfeld „Veröffentlichen“: Panel „Einstellungen“](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

### <a name="update-the-app"></a><span data-ttu-id="b0aa4-200">Aktualisieren der App</span><span class="sxs-lookup"><span data-stu-id="b0aa4-200">Update the app</span></span>

* <span data-ttu-id="b0aa4-201">Bearbeiten Sie die :::no-loc(Razor):::-Seite *Pages/Index.cshtml* , und verändern Sie deren Inhalt.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-201">Edit the *Pages/Index.cshtml* :::no-loc(Razor)::: page and change its contents.</span></span> <span data-ttu-id="b0aa4-202">Sie können beispielsweise den Absatz so verändern, dass er „Hallo ASP.NET Core!“ anzeigt.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-202">For example, you can modify the paragraph to say "Hello ASP.NET Core!":</span></span>

    [!code-html[Index](publish-to-azure-webapp-using-vs/sample/index.cshtml?highlight=10&range=1-12)]

* <span data-ttu-id="b0aa4-203">Wählen Sie erneut **Veröffentlichen** auf der Seite **Zusammenfassung des Veröffentlichungsprofils** aus.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-203">Select **Publish** from the **Publish Profile summary** page again.</span></span>

![Seite „Zusammenfassung des Veröffentlichungsprofils“](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

* <span data-ttu-id="b0aa4-205">Nachdem die Anwendung veröffentlicht wurde, vergewissern Sie sich, dass die vorgenommenen Änderungen in Azure verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-205">After the app is published, verify the changes you made are available on Azure.</span></span>

![Der Überprüfungstask ist abgeschlossen.](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a><span data-ttu-id="b0aa4-207">Bereinigen</span><span class="sxs-lookup"><span data-stu-id="b0aa4-207">Clean up</span></span>

<span data-ttu-id="b0aa4-208">Sobald Sie das Testen der App abgeschlossen haben, wechseln Sie zum [Azure-Portal](https://portal.azure.com/), und löschen Sie die App.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-208">When you have finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

* <span data-ttu-id="b0aa4-209">Wählen Sie **Ressourcengruppen** aus, und wählen Sie dann die Ressourcengruppe aus, die Sie erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-209">Select **Resource groups** , then select the resource group you created.</span></span>

![Azure-Portal: Ressourcengruppen im Menü auf der Randleiste](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* <span data-ttu-id="b0aa4-211">Wählen Sie auf der Seite **Ressourcengruppen** **Löschen** aus.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-211">In the **Resource groups** page, select **Delete**.</span></span>

![Azure-Portal: Seite „Ressourcengruppen“](publish-to-azure-webapp-using-vs/_static/rgd.png)

* <span data-ttu-id="b0aa4-213">Geben Sie den Namen der Ressourcengruppe ein, und klicken Sie auf **Löschen**.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-213">Enter the name of the resource group and select **Delete**.</span></span> <span data-ttu-id="b0aa4-214">Ihre App und alle anderen in diesem Tutorial erstellten Ressourcen werden nun aus Azure gelöscht.</span><span class="sxs-lookup"><span data-stu-id="b0aa4-214">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

### <a name="next-steps"></a><span data-ttu-id="b0aa4-215">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="b0aa4-215">Next steps</span></span>

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a><span data-ttu-id="b0aa4-216">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="b0aa4-216">Additional resources</span></span>

* <span data-ttu-id="b0aa4-217">Visual Studio Code finden Sie unter [Veröffentlichungsprofile](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span><span class="sxs-lookup"><span data-stu-id="b0aa4-217">For Visual Studio Code, see [Publish profiles](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span></span>
* [<span data-ttu-id="b0aa4-218">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="b0aa4-218">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="b0aa4-219">Azure-Ressourcengruppen</span><span class="sxs-lookup"><span data-stu-id="b0aa4-219">Azure resource groups</span></span>](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [<span data-ttu-id="b0aa4-220">Azure SQL-Datenbank</span><span class="sxs-lookup"><span data-stu-id="b0aa4-220">Azure SQL Database</span></span>](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:test/troubleshoot-azure-iis>