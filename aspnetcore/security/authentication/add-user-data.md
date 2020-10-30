---
title: 'Hinzufügen, herunterladen und Löschen von Benutzerdaten :::no-loc(Identity)::: in einem ASP.net Core Projekt'
author: rick-anderson
description: 'Erfahren Sie, wie Sie :::no-loc(Identity)::: in einem ASP.net Core Projekt benutzerdefinierte Benutzerdaten hinzufügen. Löschen von Daten pro dsgvo.'
ms.author: riande
ms.date: 03/26/2020
ms.custom: mvc, seodec18
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
uid: security/authentication/add-user-data
ms.openlocfilehash: a4e1fd780947cfa5f09fb1e03964595fa09f0f18
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061417"
---
# <a name="add-download-and-delete-custom-user-data-to-no-locidentity-in-an-aspnet-core-project"></a><span data-ttu-id="50dcb-104">Hinzufügen, herunterladen und Löschen von benutzerdefinierten Benutzerdaten :::no-loc(Identity)::: in einem ASP.net Core Projekt</span><span class="sxs-lookup"><span data-stu-id="50dcb-104">Add, download, and delete custom user data to :::no-loc(Identity)::: in an ASP.NET Core project</span></span>

<span data-ttu-id="50dcb-105">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="50dcb-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="50dcb-106">In diesem Artikel werden die folgenden Aufgaben erläutert:</span><span class="sxs-lookup"><span data-stu-id="50dcb-106">This article shows how to:</span></span>

* <span data-ttu-id="50dcb-107">Fügen Sie benutzerdefinierte Benutzerdaten zu einer ASP.net Core-Web-App hinzu.</span><span class="sxs-lookup"><span data-stu-id="50dcb-107">Add custom user data to an ASP.NET Core web app.</span></span>
* <span data-ttu-id="50dcb-108">Markieren Sie das benutzerdefinierte Benutzerdaten Modell mit dem <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PersonalDataAttribute> -Attribut, damit es automatisch heruntergeladen und gelöscht werden kann.</span><span class="sxs-lookup"><span data-stu-id="50dcb-108">Mark the custom user data model with the <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PersonalDataAttribute> attribute so it's automatically available for download and deletion.</span></span> <span data-ttu-id="50dcb-109">Das herunterladen und Löschen der Daten hilft bei [der Einhaltung der](xref:security/gdpr) dsgvo-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="50dcb-109">Making the data able to be downloaded and deleted helps meet [GDPR](xref:security/gdpr) requirements.</span></span>

<span data-ttu-id="50dcb-110">Das Projektbeispiel wird aus einer :::no-loc(Razor)::: pages-Web-App erstellt, die Anweisungen ähneln jedoch einer ASP.net Core MVC-Web-App.</span><span class="sxs-lookup"><span data-stu-id="50dcb-110">The project sample is created from a :::no-loc(Razor)::: Pages web app, but the instructions are similar for a ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="50dcb-111">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="50dcb-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="50dcb-112">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="50dcb-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-no-locrazor-web-app"></a><span data-ttu-id="50dcb-113">Erstellen einer :::no-loc(Razor)::: Web-App</span><span class="sxs-lookup"><span data-stu-id="50dcb-113">Create a :::no-loc(Razor)::: web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="50dcb-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="50dcb-114">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="50dcb-115">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt** .</span><span class="sxs-lookup"><span data-stu-id="50dcb-115">From the Visual Studio **File** menu, select **New** > **Project** .</span></span> <span data-ttu-id="50dcb-116">Benennen Sie das Projekt **"WebApp1"** Wenn Sie den Namespace des [Download Beispiel](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) Codes anpassen möchten.</span><span class="sxs-lookup"><span data-stu-id="50dcb-116">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="50dcb-117">**ASP.net Core Webanwendung** > **OK** auswählen</span><span class="sxs-lookup"><span data-stu-id="50dcb-117">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="50dcb-118">Wählen Sie in der Dropdown Liste **ASP.net Core 3,0** aus.</span><span class="sxs-lookup"><span data-stu-id="50dcb-118">Select **ASP.NET Core 3.0** in the dropdown</span></span>
* <span data-ttu-id="50dcb-119">**Webanwendung** auswählen > **OK**</span><span class="sxs-lookup"><span data-stu-id="50dcb-119">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="50dcb-120">Erstellen Sie das Projekt, und führen Sie es aus.</span><span class="sxs-lookup"><span data-stu-id="50dcb-120">Build and run the project.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="50dcb-121">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt** .</span><span class="sxs-lookup"><span data-stu-id="50dcb-121">From the Visual Studio **File** menu, select **New** > **Project** .</span></span> <span data-ttu-id="50dcb-122">Benennen Sie das Projekt **"WebApp1"** Wenn Sie den Namespace des [Download Beispiel](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) Codes anpassen möchten.</span><span class="sxs-lookup"><span data-stu-id="50dcb-122">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="50dcb-123">**ASP.net Core Webanwendung** > **OK** auswählen</span><span class="sxs-lookup"><span data-stu-id="50dcb-123">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="50dcb-124">Wählen Sie in der Dropdown Liste **ASP.net Core 2,2** aus.</span><span class="sxs-lookup"><span data-stu-id="50dcb-124">Select **ASP.NET Core 2.2** in the dropdown</span></span>
* <span data-ttu-id="50dcb-125">**Webanwendung** auswählen > **OK**</span><span class="sxs-lookup"><span data-stu-id="50dcb-125">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="50dcb-126">Erstellen Sie das Projekt, und führen Sie es aus.</span><span class="sxs-lookup"><span data-stu-id="50dcb-126">Build and run the project.</span></span>

::: moniker-end


# <a name="net-core-cli"></a>[<span data-ttu-id="50dcb-127">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="50dcb-127">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-no-locidentity-scaffolder"></a><span data-ttu-id="50dcb-128">Das :::no-loc(Identity)::: Gerüst ausführen</span><span class="sxs-lookup"><span data-stu-id="50dcb-128">Run the :::no-loc(Identity)::: scaffolder</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="50dcb-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="50dcb-129">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="50dcb-130">Klicken Sie in **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, > **Add**  >  **Neues Gerüst Element** hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="50dcb-130">From **Solution Explorer** , right-click on the project > **Add** > **New Scaffolded Item** .</span></span>
* <span data-ttu-id="50dcb-131">Wählen Sie im linken Bereich des Dialog Felds **Gerüst hinzufügen** die Option **:::no-loc(Identity):::**  >  **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="50dcb-131">From the left pane of the **Add Scaffold** dialog, select **:::no-loc(Identity):::** > **Add** .</span></span>
* <span data-ttu-id="50dcb-132">Im Dialog **Feld :::no-loc(Identity)::: Hinzufügen** werden die folgenden Optionen angezeigt:</span><span class="sxs-lookup"><span data-stu-id="50dcb-132">In the **Add :::no-loc(Identity):::** dialog, the following options:</span></span>
  * <span data-ttu-id="50dcb-133">Wählen Sie die vorhandene Layoutdatei aus  *~/pages/Shared/_Layout. cshtml*</span><span class="sxs-lookup"><span data-stu-id="50dcb-133">Select the existing layout  file  *~/Pages/Shared/_Layout.cshtml*</span></span>
  * <span data-ttu-id="50dcb-134">Wählen Sie die folgenden zu über schreibenden Dateien aus:</span><span class="sxs-lookup"><span data-stu-id="50dcb-134">Select the following files to override:</span></span>
    * <span data-ttu-id="50dcb-135">**Konto/Registrierung**</span><span class="sxs-lookup"><span data-stu-id="50dcb-135">**Account/Register**</span></span>
    * <span data-ttu-id="50dcb-136">**Konto/Verwaltung/Index**</span><span class="sxs-lookup"><span data-stu-id="50dcb-136">**Account/Manage/Index**</span></span>
  * <span data-ttu-id="50dcb-137">Wählen Sie die **+** Schaltfläche aus, um eine neue **Datenkontext Klasse** zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="50dcb-137">Select the **+** button to create a new **Data context class** .</span></span> <span data-ttu-id="50dcb-138">Akzeptieren Sie den Typ ( **"WebApp1". Models. WebApp1Context** , wenn das Projekt den Namen **"WebApp1"** hat).</span><span class="sxs-lookup"><span data-stu-id="50dcb-138">Accept the type ( **WebApp1.Models.WebApp1Context** if the project is named **WebApp1** ).</span></span>
  * <span data-ttu-id="50dcb-139">Wählen Sie die **+** Schaltfläche aus, um eine neue **Benutzerklasse** zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="50dcb-139">Select the **+** button to create a new **User class** .</span></span> <span data-ttu-id="50dcb-140">Akzeptieren Sie den Typ ( **WebApp1User** , wenn das Projekt den Namen **"WebApp1"** hat), > **Hinzufügen** .</span><span class="sxs-lookup"><span data-stu-id="50dcb-140">Accept the type ( **WebApp1User** if the project is named **WebApp1** ) > **Add** .</span></span>
* <span data-ttu-id="50dcb-141">Wählen Sie **Hinzufügen** .</span><span class="sxs-lookup"><span data-stu-id="50dcb-141">Select **Add** .</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="50dcb-142">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="50dcb-142">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="50dcb-143">Wenn Sie das ASP.net Core Gerüst nicht bereits installiert haben, installieren Sie es jetzt:</span><span class="sxs-lookup"><span data-stu-id="50dcb-143">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="50dcb-144">Fügen Sie der Projektdatei (. csproj) einen Paket Verweis auf [Microsoft. VisualStudio. Web. CodeGeneration. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) hinzu.</span><span class="sxs-lookup"><span data-stu-id="50dcb-144">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (.csproj) file.</span></span> <span data-ttu-id="50dcb-145">Führen Sie den folgenden Befehl im Projektverzeichnis aus:</span><span class="sxs-lookup"><span data-stu-id="50dcb-145">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="50dcb-146">Führen Sie den folgenden Befehl aus, um die :::no-loc(Identity)::: Gerüst Optionen aufzulisten:</span><span class="sxs-lookup"><span data-stu-id="50dcb-146">Run the following command to list the :::no-loc(Identity)::: scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="50dcb-147">Führen Sie im Projektordner das :::no-loc(Identity)::: Gerüst aus:</span><span class="sxs-lookup"><span data-stu-id="50dcb-147">In the project folder, run the :::no-loc(Identity)::: scaffolder:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

<span data-ttu-id="50dcb-148">Befolgen Sie die Anweisungen in [Migrationen, UseAuthentication und Layout](xref:security/authentication/scaffold-identity#efm) , um die folgenden Schritte auszuführen:</span><span class="sxs-lookup"><span data-stu-id="50dcb-148">Follow the instruction in [Migrations, UseAuthentication, and layout](xref:security/authentication/scaffold-identity#efm) to perform the following steps:</span></span>

* <span data-ttu-id="50dcb-149">Erstellen Sie eine Migration, und aktualisieren Sie die Datenbank.</span><span class="sxs-lookup"><span data-stu-id="50dcb-149">Create a migration and update the database.</span></span>
* <span data-ttu-id="50dcb-150">`UseAuthentication` wurde `Startup.Configure` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="50dcb-150">Add `UseAuthentication` to `Startup.Configure`.</span></span>
* <span data-ttu-id="50dcb-151">Fügen Sie `<partial name="_LoginPartial" />` der Layoutdatei hinzu.</span><span class="sxs-lookup"><span data-stu-id="50dcb-151">Add `<partial name="_LoginPartial" />` to the layout file.</span></span>
* <span data-ttu-id="50dcb-152">Testen der App:</span><span class="sxs-lookup"><span data-stu-id="50dcb-152">Test the app:</span></span>
  * <span data-ttu-id="50dcb-153">Registrieren eines Benutzers</span><span class="sxs-lookup"><span data-stu-id="50dcb-153">Register a user</span></span>
  * <span data-ttu-id="50dcb-154">Wählen Sie den neuen Benutzernamen (neben dem Link **Logout) aus** .</span><span class="sxs-lookup"><span data-stu-id="50dcb-154">Select the new user name (next to the **Logout** link).</span></span> <span data-ttu-id="50dcb-155">Möglicherweise müssen Sie das Fenster erweitern oder das Navigationsleisten Symbol auswählen, um den Benutzernamen und andere Links anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="50dcb-155">You might need to expand the window or select the navigation bar icon to show the user name and other links.</span></span>
  * <span data-ttu-id="50dcb-156">Wählen Sie die Registerkarte **persönliche Daten** aus.</span><span class="sxs-lookup"><span data-stu-id="50dcb-156">Select the **Personal Data** tab.</span></span>
  * <span data-ttu-id="50dcb-157">Wählen Sie die Schaltfläche **herunterladen** aus, und untersuchen Sie die Datei *PersonalData.js*</span><span class="sxs-lookup"><span data-stu-id="50dcb-157">Select the **Download** button and examined the *PersonalData.json* file.</span></span>
  * <span data-ttu-id="50dcb-158">Testen Sie die Schaltfläche **Löschen** , mit der der angemeldete Benutzer gelöscht wird.</span><span class="sxs-lookup"><span data-stu-id="50dcb-158">Test the **Delete** button, which deletes the logged on user.</span></span>

## <a name="add-custom-user-data-to-the-no-locidentity-db"></a><span data-ttu-id="50dcb-159">Hinzufügen von benutzerdefinierten Benutzer :::no-loc(Identity)::: Daten zur Datenbank</span><span class="sxs-lookup"><span data-stu-id="50dcb-159">Add custom user data to the :::no-loc(Identity)::: DB</span></span>

<span data-ttu-id="50dcb-160">Aktualisieren Sie die `:::no-loc(Identity):::User` abgeleitete Klasse mit benutzerdefinierten Eigenschaften.</span><span class="sxs-lookup"><span data-stu-id="50dcb-160">Update the `:::no-loc(Identity):::User` derived class with custom properties.</span></span> <span data-ttu-id="50dcb-161">Wenn Sie das Projekt "WebApp1" benannt haben, heißt die Datei " *Areas/ :::no-loc(Identity)::: /Data/WebApp1User.cs* ".</span><span class="sxs-lookup"><span data-stu-id="50dcb-161">If you named the project WebApp1, the file is named *Areas/:::no-loc(Identity):::/Data/WebApp1User.cs* .</span></span> <span data-ttu-id="50dcb-162">Aktualisieren Sie die Datei mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="50dcb-162">Update the file with the following code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/:::no-loc(Identity):::/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/:::no-loc(Identity):::/Data/WebApp1User.cs)]

::: moniker-end

<span data-ttu-id="50dcb-163">Eigenschaften mit dem Attribut " [Personal Data](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) ":</span><span class="sxs-lookup"><span data-stu-id="50dcb-163">Properties with the [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) attribute are:</span></span>

* <span data-ttu-id="50dcb-164">Wird gelöscht, wenn die Seite " *Bereiche/ :::no-loc(Identity)::: /pages/Account/Manage/DeletePersonalData.cshtml* " :::no-loc(Razor)::: aufruft `UserManager.Delete` .</span><span class="sxs-lookup"><span data-stu-id="50dcb-164">Deleted when the *Areas/:::no-loc(Identity):::/Pages/Account/Manage/DeletePersonalData.cshtml* :::no-loc(Razor)::: Page calls `UserManager.Delete`.</span></span>
* <span data-ttu-id="50dcb-165">In den heruntergeladenen Daten von der Seite " *Bereiche/ :::no-loc(Identity)::: /pages/Account/Manage/DownloadPersonalData.cshtml* " enthalten :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="50dcb-165">Included in the downloaded data by the *Areas/:::no-loc(Identity):::/Pages/Account/Manage/DownloadPersonalData.cshtml* :::no-loc(Razor)::: Page.</span></span>

### <a name="update-the-accountmanageindexcshtml-page"></a><span data-ttu-id="50dcb-166">Aktualisieren Sie die Seite "Account/Manage/Index. cshtml".</span><span class="sxs-lookup"><span data-stu-id="50dcb-166">Update the Account/Manage/Index.cshtml page</span></span>

<span data-ttu-id="50dcb-167">Aktualisieren Sie die `InputModel` in *Areas/ :::no-loc(Identity)::: /pages/Account/Manage/Index.cshtml.cs* mit dem folgenden markierten Code:</span><span class="sxs-lookup"><span data-stu-id="50dcb-167">Update the `InputModel` in *Areas/:::no-loc(Identity):::/Pages/Account/Manage/Index.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/:::no-loc(Identity):::/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

<span data-ttu-id="50dcb-168">Aktualisieren Sie die *Bereiche/ :::no-loc(Identity)::: /pages/Account/Manage/Index.cshtml* mit dem folgenden markierten Markup:</span><span class="sxs-lookup"><span data-stu-id="50dcb-168">Update the *Areas/:::no-loc(Identity):::/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/:::no-loc(Identity):::/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/:::no-loc(Identity):::/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

<span data-ttu-id="50dcb-169">Aktualisieren Sie die *Bereiche/ :::no-loc(Identity)::: /pages/Account/Manage/Index.cshtml* mit dem folgenden markierten Markup:</span><span class="sxs-lookup"><span data-stu-id="50dcb-169">Update the *Areas/:::no-loc(Identity):::/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/:::no-loc(Identity):::/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a><span data-ttu-id="50dcb-170">Aktualisieren Sie die Seite "Account/Register. cshtml".</span><span class="sxs-lookup"><span data-stu-id="50dcb-170">Update the Account/Register.cshtml page</span></span>

<span data-ttu-id="50dcb-171">Aktualisieren Sie die `InputModel` in *Areas/ :::no-loc(Identity)::: /pages/Account/Register.cshtml.cs* mit dem folgenden markierten Code:</span><span class="sxs-lookup"><span data-stu-id="50dcb-171">Update the `InputModel` in *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

<span data-ttu-id="50dcb-172">Aktualisieren Sie die *Bereiche/ :::no-loc(Identity)::: /pages/Account/Register.cshtml* mit dem folgenden markierten Markup:</span><span class="sxs-lookup"><span data-stu-id="50dcb-172">Update the *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

<span data-ttu-id="50dcb-173">Aktualisieren Sie die *Bereiche/ :::no-loc(Identity)::: /pages/Account/Register.cshtml* mit dem folgenden markierten Markup:</span><span class="sxs-lookup"><span data-stu-id="50dcb-173">Update the *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


<span data-ttu-id="50dcb-174">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="50dcb-174">Build the project.</span></span>

### <a name="add-a-migration-for-the-custom-user-data"></a><span data-ttu-id="50dcb-175">Hinzufügen einer Migration für die benutzerdefinierten Benutzerdaten</span><span class="sxs-lookup"><span data-stu-id="50dcb-175">Add a migration for the custom user data</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="50dcb-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="50dcb-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="50dcb-177">In der Visual Studio- **Paket-Manager-Konsole** :</span><span class="sxs-lookup"><span data-stu-id="50dcb-177">In the Visual Studio **Package Manager Console** :</span></span>

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="50dcb-178">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="50dcb-178">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a><span data-ttu-id="50dcb-179">Test erstellen, anzeigen, herunterladen, benutzerdefinierte Benutzerdaten löschen</span><span class="sxs-lookup"><span data-stu-id="50dcb-179">Test create, view, download, delete custom user data</span></span>

<span data-ttu-id="50dcb-180">Testen der App:</span><span class="sxs-lookup"><span data-stu-id="50dcb-180">Test the app:</span></span>

* <span data-ttu-id="50dcb-181">Registrieren Sie einen neuen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="50dcb-181">Register a new user.</span></span>
* <span data-ttu-id="50dcb-182">Zeigen Sie die benutzerdefinierten Benutzerdaten auf der `/:::no-loc(Identity):::/Account/Manage` Seite an.</span><span class="sxs-lookup"><span data-stu-id="50dcb-182">View the custom user data on the `/:::no-loc(Identity):::/Account/Manage` page.</span></span>
* <span data-ttu-id="50dcb-183">Herunterladen und Anzeigen der persönlichen Daten der Benutzer auf der `/:::no-loc(Identity):::/Account/Manage/PersonalData` Seite.</span><span class="sxs-lookup"><span data-stu-id="50dcb-183">Download and view the users personal data from the `/:::no-loc(Identity):::/Account/Manage/PersonalData` page.</span></span>

## <a name="add-claims-to-no-locidentity-using-iuserclaimsprincipalfactoryapplicationuser"></a><span data-ttu-id="50dcb-184">Hinzufügen von Ansprüchen zu :::no-loc(Identity)::: mithilfe von iuserclaimsprincipalfactory<ApplicationUser></span><span class="sxs-lookup"><span data-stu-id="50dcb-184">Add claims to :::no-loc(Identity)::: using IUserClaimsPrincipalFactory<ApplicationUser></span></span>

> [!NOTE]
> <span data-ttu-id="50dcb-185">Dieser Abschnitt ist keine Erweiterung des vorherigen Tutorials.</span><span class="sxs-lookup"><span data-stu-id="50dcb-185">This section isn't an extension of the previous tutorial.</span></span> <span data-ttu-id="50dcb-186">Informationen zum Anwenden der folgenden Schritte auf die APP, die mit dem Tutorial erstellt wurde, finden Sie in [diesem GitHub-Problem](https://github.com/dotnet/AspNetCore.Docs/issues/18797).</span><span class="sxs-lookup"><span data-stu-id="50dcb-186">To apply the following steps to the app built using the tutorial, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/18797).</span></span>

<span data-ttu-id="50dcb-187">Zusätzliche Ansprüche können :::no-loc(ASP.NET Core Identity)::: mithilfe der-Schnittstelle hinzugefügt werden `IUserClaimsPrincipalFactory<T>` .</span><span class="sxs-lookup"><span data-stu-id="50dcb-187">Additional claims can be added to :::no-loc(ASP.NET Core Identity)::: by using the `IUserClaimsPrincipalFactory<T>` interface.</span></span> <span data-ttu-id="50dcb-188">Diese Klasse kann der app in der-Methode hinzugefügt werden `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="50dcb-188">This class can be added to the app in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="50dcb-189">Fügen Sie die benutzerdefinierte Implementierung der-Klasse wie folgt hinzu:</span><span class="sxs-lookup"><span data-stu-id="50dcb-189">Add the custom implementation of the class as follows:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Add:::no-loc(Identity):::<ApplicationUser, :::no-loc(Identity):::Role>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddScoped<IUserClaimsPrincipalFactory<ApplicationUser>, 
        AdditionalUserClaimsPrincipalFactory>();
```

<span data-ttu-id="50dcb-190">Der Democode verwendet die- `ApplicationUser` Klasse.</span><span class="sxs-lookup"><span data-stu-id="50dcb-190">The demo code uses the `ApplicationUser` class.</span></span> <span data-ttu-id="50dcb-191">Diese Klasse fügt eine `IsAdmin` Eigenschaft hinzu, die zum Hinzufügen des zusätzlichen Anspruchs verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="50dcb-191">This class adds an `IsAdmin` property which is used to add the additional claim.</span></span>

```csharp
public class ApplicationUser : :::no-loc(Identity):::User
{
    public bool IsAdmin { get; set; }
}
```

<span data-ttu-id="50dcb-192">Die `AdditionalUserClaimsPrincipalFactory` implementiert die `UserClaimsPrincipalFactory`-Schnittstelle.</span><span class="sxs-lookup"><span data-stu-id="50dcb-192">The `AdditionalUserClaimsPrincipalFactory` implements the `UserClaimsPrincipalFactory` interface.</span></span> <span data-ttu-id="50dcb-193">Dem wird ein neuer Rollen Anspruch hinzugefügt `ClaimsPrincipal` .</span><span class="sxs-lookup"><span data-stu-id="50dcb-193">A new role claim is added to the `ClaimsPrincipal`.</span></span>

```csharp
public class AdditionalUserClaimsPrincipalFactory 
        : UserClaimsPrincipalFactory<ApplicationUser, :::no-loc(Identity):::Role>
{
    public AdditionalUserClaimsPrincipalFactory( 
        UserManager<ApplicationUser> userManager,
        RoleManager<:::no-loc(Identity):::Role> roleManager, 
        IOptions<:::no-loc(Identity):::Options> optionsAccessor) 
        : base(userManager, roleManager, optionsAccessor)
    {}

    public async override Task<ClaimsPrincipal> CreateAsync(ApplicationUser user)
    {
        var principal = await base.CreateAsync(user);
        var identity = (Claims:::no-loc(Identity):::)principal.:::no-loc(Identity):::;

        var claims = new List<Claim>();
        if (user.IsAdmin)
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "admin"));
        }
        else
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "user"));
        }

        identity.AddClaims(claims);
        return principal;
    }
}
```

<span data-ttu-id="50dcb-194">Der zusätzliche Anspruch kann dann in der APP verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="50dcb-194">The additional claim can then be used in the app.</span></span> <span data-ttu-id="50dcb-195">Auf einer :::no-loc(Razor)::: Seite kann die `IAuthorizationService` Instanz verwendet werden, um auf den Anspruchs Wert zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="50dcb-195">In a :::no-loc(Razor)::: Page, the `IAuthorizationService` instance can be used to access the claim value.</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

@if ((await AuthorizationService.AuthorizeAsync(User, "IsAdmin")).Succeeded)
{
    <ul class="mr-auto navbar-nav">
        <li class="nav-item">
            <a class="nav-link" asp-controller="Admin" asp-action="Index">ADMIN</a>
        </li>
    </ul>
}
```
