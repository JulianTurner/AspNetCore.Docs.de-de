---
title: Sichern einer eigenständigen ASP.NET Core Blazor WebAssembly-App mit der Authentifizierungsbibliothek
author: guardrex
description: In diesem Artikel erfahren Sie, wie Sie eine eigenständige ASP.NET Core Blazor WebAssembly-App mit der Authentifizierungsbibliothek sichern.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
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
uid: blazor/security/webassembly/standalone-with-authentication-library
ms.openlocfilehash: b16533ef662b341053498162ce4ecb62445f2061
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "97854423"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="d868f-103">Sichern einer eigenständigen ASP.NET Core Blazor WebAssembly-App mit der Authentifizierungsbibliothek</span><span class="sxs-lookup"><span data-stu-id="d868f-103">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="d868f-104">Von [Javier Calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d868f-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d868f-105">*Befolgen Sie die Anweisungen in diesem Artikel nicht für Azure Active Directory (AAD) und Azure Active Directory B2C (AAD B2C). Informationen zu AAD und AAD B2C finden Sie in den entsprechenden Artikeln in diesem Inhaltsverzeichnisknoten.*</span><span class="sxs-lookup"><span data-stu-id="d868f-105">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="d868f-106">Befolgen Sie die entsprechenden Anleitungen für die Tools Ihrer Wahl, um eine [eigenständige Blazor WebAssembly-App](xref:blazor/hosting-models#blazor-webassembly) zu erstellen, die die [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication)-Bibliothek verwendet.</span><span class="sxs-lookup"><span data-stu-id="d868f-106">To create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library, follow the guidance for your choice of tooling.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d868f-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d868f-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d868f-108">So erstellen Sie ein neues Blazor WebAssembly-Projekt mit einem Authentifizierungsmechanismus:</span><span class="sxs-lookup"><span data-stu-id="d868f-108">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="d868f-109">Nachdem Sie im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Vorlage **Blazor WebAssembly-App** ausgewählt haben, wählen Sie im Dialogfeld **Authentifizierung** die Option **Ändern** aus.</span><span class="sxs-lookup"><span data-stu-id="d868f-109">After choosing the **Blazor WebAssembly App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

1. <span data-ttu-id="d868f-110">Wählen Sie **Einzelne Benutzerkonten** mit der Option **In-App-Speicherung von Benutzerkonten** aus, um Benutzer über das [Identity](xref:security/authentication/identity)-System von ASP.NET Core innerhalb der App zu speichern.</span><span class="sxs-lookup"><span data-stu-id="d868f-110">Select **Individual User Accounts** with the **Store user accounts in-app** option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="d868f-111">Visual Studio Code / .NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="d868f-111">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="d868f-112">Erstellen Sie ein neues Blazor WebAssembly-Projekt mit einem Authentifizierungsmechanismus in einem leeren Ordner.</span><span class="sxs-lookup"><span data-stu-id="d868f-112">Create a new Blazor WebAssembly project with an authentication mechanism in an empty folder.</span></span> <span data-ttu-id="d868f-113">Geben Sie den Authentifizierungsmechanismus `Individual` mit der Option `-au|--auth` an, um Benutzer über das [Identity](xref:security/authentication/identity)-System von ASP.NET Core innerhalb der App zu speichern:</span><span class="sxs-lookup"><span data-stu-id="d868f-113">Specify the `Individual` authentication mechanism with the `-au|--auth` option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -o {APP NAME}
```

| <span data-ttu-id="d868f-114">Platzhalter</span><span class="sxs-lookup"><span data-stu-id="d868f-114">Placeholder</span></span>  | <span data-ttu-id="d868f-115">Beispiel</span><span class="sxs-lookup"><span data-stu-id="d868f-115">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

<span data-ttu-id="d868f-116">Der mit der Option `-o|--output` angegebene Ausgabespeicherort erstellt einen Projektordner, sofern kein solcher vorhanden ist, und wird Teil des Namens der App.</span><span class="sxs-lookup"><span data-stu-id="d868f-116">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="d868f-117">Weitere Informationen finden Sie im Befehl [`dotnet new`](/dotnet/core/tools/dotnet-new) im Leitfaden für .NET Core.</span><span class="sxs-lookup"><span data-stu-id="d868f-117">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d868f-118">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d868f-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d868f-119">So erstellen Sie ein neues Blazor WebAssembly-Projekt mit einem Authentifizierungsmechanismus:</span><span class="sxs-lookup"><span data-stu-id="d868f-119">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="d868f-120">Wählen Sie im Schritt **Konfigurieren Ihrer neuen Blazor WebAssembly-App** in der Dropdownliste **Authentifizierung** die Option **Individual Authentication (in-app)** (Individuelle Authentifizierung [in der App]) aus.</span><span class="sxs-lookup"><span data-stu-id="d868f-120">On the **Configure your new Blazor WebAssembly App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="d868f-121">Die App wird für einzelne Benutzer erstellt, die über [Identity](xref:security/authentication/identity) von ASP.NET Core in der App gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="d868f-121">The app is created for individual users stored in the app with ASP.NET Core [Identity](xref:security/authentication/identity).</span></span>

---

## <a name="authentication-package"></a><span data-ttu-id="d868f-122">Authentifizierungspaket</span><span class="sxs-lookup"><span data-stu-id="d868f-122">Authentication package</span></span>

<span data-ttu-id="d868f-123">Wenn eine App erstellt wird, die einzelne Benutzerkonten verwendet, erhält die App automatisch einen Paketverweis für das [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication)-Paket in der Projektdatei der App.</span><span class="sxs-lookup"><span data-stu-id="d868f-123">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package in the app's project file.</span></span> <span data-ttu-id="d868f-124">Das Paket stellt einige Primitive bereit, die der App beim Authentifizieren von Benutzern und beim Abrufen von Token zum Aufrufen geschützter APIs helfen.</span><span class="sxs-lookup"><span data-stu-id="d868f-124">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="d868f-125">Wenn Sie einer App eine Authentifizierung hinzufügen, fügen Sie das Paket manuell der Projektdatei der App hinzu:</span><span class="sxs-lookup"><span data-stu-id="d868f-125">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

<span data-ttu-id="d868f-126">Für den Platzhalter `{VERSION}` kann die letzte stabile Version des Pakets, die mit der Version des gemeinsam genutzten Frameworks der Anwendung übereinstimmt, im **Versionsverlauf** des Pakets auf [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) ermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="d868f-126">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="d868f-127">Unterstützung für Authentifizierungsdienste</span><span class="sxs-lookup"><span data-stu-id="d868f-127">Authentication service support</span></span>

<span data-ttu-id="d868f-128">Die Unterstützung für die Authentifizierung von Benutzern wird im Dienstcontainer mit der vom Paket [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) bereitgestellten <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>-Erweiterungsmethode registriert.</span><span class="sxs-lookup"><span data-stu-id="d868f-128">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> extension method provided by the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package.</span></span> <span data-ttu-id="d868f-129">Diese Methode richtet die Dienste ein, die erforderlich sind, damit die App mit dem Identitätsanbieter interagiert.</span><span class="sxs-lookup"><span data-stu-id="d868f-129">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="d868f-130">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="d868f-130">`Program.cs`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

<span data-ttu-id="d868f-131">Die Konfiguration wird durch die Datei `wwwroot/appsettings.json` bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="d868f-131">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

<span data-ttu-id="d868f-132">Für die Authentifizierungsunterstützung für eigenständige Apps wird OpenID Connect (OIDC) verwendet.</span><span class="sxs-lookup"><span data-stu-id="d868f-132">Authentication support for standalone apps is offered using OpenID Connect (OIDC).</span></span> <span data-ttu-id="d868f-133">Die <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>-Methode akzeptiert einen Rückruf zum Konfigurieren der für die Authentifizierung einer App mit OIDC erforderlichen Parameter.</span><span class="sxs-lookup"><span data-stu-id="d868f-133">The <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="d868f-134">Die für das Konfigurieren der App erforderlichen Werte können über die OIDC-kompatible IP-Adresse abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="d868f-134">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="d868f-135">Rufen Sie die Werte ab, wenn Sie die App registrieren. Dies erfolgt in der Regel im entsprechenden Onlineportal.</span><span class="sxs-lookup"><span data-stu-id="d868f-135">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="d868f-136">Zugriffstokenbereiche</span><span class="sxs-lookup"><span data-stu-id="d868f-136">Access token scopes</span></span>

<span data-ttu-id="d868f-137">Mit der Blazor WebAssembly-Vorlage werden automatisch Standardbereiche für `openid` und `profile` konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="d868f-137">The Blazor WebAssembly template automatically configures default scopes for `openid` and `profile`.</span></span>

<span data-ttu-id="d868f-138">Die Blazor WebAssembly-Vorlage konfiguriert die App nicht automatisch so, dass diese ein Zugriffstoken für eine sichere API anfordert.</span><span class="sxs-lookup"><span data-stu-id="d868f-138">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="d868f-139">Zum Bereitstellen eines Zugriffstokens als Teil des Anmeldeflows fügen Sie den Bereich den Standardtokenbereichen von <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions> hinzu:</span><span class="sxs-lookup"><span data-stu-id="d868f-139">To provision an access token as part of the sign-in flow, add the scope to the default token scopes of the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="d868f-140">Weitere Informationen finden Sie in den folgenden Abschnitten des Artikels zu *zusätzlichen Szenarios*:</span><span class="sxs-lookup"><span data-stu-id="d868f-140">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="d868f-141">Anfordern zusätzlicher Zugriffstoken</span><span class="sxs-lookup"><span data-stu-id="d868f-141">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="d868f-142">Anfügen von Token an ausgehende Anforderungen</span><span class="sxs-lookup"><span data-stu-id="d868f-142">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="d868f-143">Imports-Datei</span><span class="sxs-lookup"><span data-stu-id="d868f-143">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="d868f-144">Indexseite</span><span class="sxs-lookup"><span data-stu-id="d868f-144">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="d868f-145">App-Komponente</span><span class="sxs-lookup"><span data-stu-id="d868f-145">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="d868f-146">RedirectToLogin-Komponente</span><span class="sxs-lookup"><span data-stu-id="d868f-146">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="d868f-147">LoginDisplay-Komponente</span><span class="sxs-lookup"><span data-stu-id="d868f-147">LoginDisplay component</span></span>

<span data-ttu-id="d868f-148">Die `LoginDisplay`-Komponente (`Shared/LoginDisplay.razor`) wird in der `MainLayout`-Komponente (`Shared/MainLayout.razor`) gerendert, und sie verwaltet die folgenden Verhaltensweisen:</span><span class="sxs-lookup"><span data-stu-id="d868f-148">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="d868f-149">Für authentifizierte Benutzer:</span><span class="sxs-lookup"><span data-stu-id="d868f-149">For authenticated users:</span></span>
  * <span data-ttu-id="d868f-150">Zeigt den aktuellen Benutzernamen an</span><span class="sxs-lookup"><span data-stu-id="d868f-150">Displays the current username.</span></span>
  * <span data-ttu-id="d868f-151">Bietet eine Schaltfläche zum Abmelden von der App</span><span class="sxs-lookup"><span data-stu-id="d868f-151">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="d868f-152">Bietet die Option zur Anmeldung für anonyme Benutzer</span><span class="sxs-lookup"><span data-stu-id="d868f-152">For anonymous users, offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

## <a name="authentication-component"></a><span data-ttu-id="d868f-153">Authentication-Komponente</span><span class="sxs-lookup"><span data-stu-id="d868f-153">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="d868f-154">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="d868f-154">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="d868f-155">Nicht authentifizierte oder nicht autorisierte Web-API-Anforderungen in einer App mit einem sicheren Standardclient</span><span class="sxs-lookup"><span data-stu-id="d868f-155">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
