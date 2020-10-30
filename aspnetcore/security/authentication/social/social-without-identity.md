---
title: 'Authentifizierung mit Facebook, Google und externem Anbieter ohne :::no-loc(ASP.NET Core Identity):::'
author: rick-anderson
description: 'Eine Erläuterung der Verwendung von Facebook, Google, Twitter usw. Kontobenutzer Authentifizierung ohne :::no-loc(ASP.NET Core Identity)::: .'
ms.author: riande
ms.date: 12/10/2019
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
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: cd7545a3ddaccedfa64ef5e9d5458c21c651257a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060286"
---
# <a name="use-social-sign-in-provider-authentication-without-no-locaspnet-core-identity"></a><span data-ttu-id="c0874-103">Verwenden Sie die Authentifizierung für den sozialen Anmelde Anbieter ohne :::no-loc(ASP.NET Core Identity):::</span><span class="sxs-lookup"><span data-stu-id="c0874-103">Use social sign-in provider authentication without :::no-loc(ASP.NET Core Identity):::</span></span>

<span data-ttu-id="c0874-104">Von [Kirk Larkin](https://twitter.com/serpent5) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c0874-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c0874-105"><xref:security/authentication/social/index> Beschreibt, wie Sie es Benutzern ermöglichen können, sich mithilfe von OAuth 2,0 mit Anmelde Informationen von externen Authentifizierungs Anbietern anzumelden.</span><span class="sxs-lookup"><span data-stu-id="c0874-105"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="c0874-106">Die in diesem Thema beschriebene Vorgehensweise umfasst :::no-loc(ASP.NET Core Identity)::: als Authentifizierungs Anbieter.</span><span class="sxs-lookup"><span data-stu-id="c0874-106">The approach described in that topic includes :::no-loc(ASP.NET Core Identity)::: as an authentication provider.</span></span>

<span data-ttu-id="c0874-107">In diesem Beispiel wird veranschaulicht, wie ein externer Authentifizierungs Anbieter **ohne** verwendet wird :::no-loc(ASP.NET Core Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="c0874-107">This sample demonstrates how to use an external authentication provider **without** :::no-loc(ASP.NET Core Identity):::.</span></span> <span data-ttu-id="c0874-108">Dies ist nützlich für apps, die nicht alle Features von benötigen :::no-loc(ASP.NET Core Identity)::: , aber trotzdem eine Integration in einen vertrauenswürdigen externen Authentifizierungs Anbieter erfordern.</span><span class="sxs-lookup"><span data-stu-id="c0874-108">This is useful for apps that don't require all of the features of :::no-loc(ASP.NET Core Identity):::, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="c0874-109">In diesem Beispiel wird die [Google-Authentifizierung](xref:security/authentication/google-logins) zum Authentifizieren von Benutzern verwendet.</span><span class="sxs-lookup"><span data-stu-id="c0874-109">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="c0874-110">Die Verwendung der Google-Authentifizierung verlagert viele der Komplexitäten bei der Verwaltung des Anmeldevorgangs an Google.</span><span class="sxs-lookup"><span data-stu-id="c0874-110">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="c0874-111">Informationen zur Integration in einen anderen externen Authentifizierungs Anbieter finden Sie in den folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="c0874-111">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="c0874-112">Facebook-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="c0874-112">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="c0874-113">Microsoft-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="c0874-113">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="c0874-114">Twitter-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="c0874-114">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="c0874-115">Andere Anbieter</span><span class="sxs-lookup"><span data-stu-id="c0874-115">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="c0874-116">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="c0874-116">Configuration</span></span>

<span data-ttu-id="c0874-117">Konfigurieren Sie in der `ConfigureServices` -Methode die Authentifizierungs Schemas der APP mit den <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*> Methoden, und <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> :</span><span class="sxs-lookup"><span data-stu-id="c0874-117">In the `ConfigureServices` method, configure the app's authentication schemes with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>, <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Cookie):::Extensions.Add:::no-loc(Cookie):::*>, and <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

<span data-ttu-id="c0874-118">Der-Befehl ruft <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> die der APP auf <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme> .</span><span class="sxs-lookup"><span data-stu-id="c0874-118">The call to <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> sets the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>.</span></span> <span data-ttu-id="c0874-119">Das `DefaultScheme` ist das Standardschema, das von den folgenden `HttpContext` Authentifizierungs Erweiterungs Methoden verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="c0874-119">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="c0874-120">Wenn Sie die APP `DefaultScheme` auf [ :::no-loc(Cookie)::: authenticationdefaults. authenticationScheme](xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme) (" :::no-loc(Cookie)::: s") festlegen, wird die APP so konfiguriert, :::no-loc(Cookie)::: dass Sie s als Standardschema für diese Erweiterungs Methoden verwendet.</span><span class="sxs-lookup"><span data-stu-id="c0874-120">Setting the app's `DefaultScheme` to [:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme) (":::no-loc(Cookie):::s") configures the app to use :::no-loc(Cookie):::s as the default scheme for these extension methods.</span></span> <span data-ttu-id="c0874-121">Wenn Sie die APP <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> auf [googledefaults. authenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") festlegen, wird die APP für die Verwendung von Google als Standardschema für Aufrufe von konfiguriert `ChallengeAsync` .</span><span class="sxs-lookup"><span data-stu-id="c0874-121">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="c0874-122">`DefaultChallengeScheme` überschreibt `DefaultScheme` .</span><span class="sxs-lookup"><span data-stu-id="c0874-122">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="c0874-123"><xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>Weitere Eigenschaften, die bei Festlegung überschreiben, finden Sie unter `DefaultScheme` .</span><span class="sxs-lookup"><span data-stu-id="c0874-123">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="c0874-124">`Startup.Configure`Rufen Sie in `UseAuthentication` und zwischen dem Aufruf `UseAuthorization` von und auf `UseRouting` `UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="c0874-124">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` between calling `UseRouting` and `UseEndpoints`.</span></span> <span data-ttu-id="c0874-125">Dadurch wird die `HttpContext.User` -Eigenschaft festgelegt und die Autorisierungs Middleware für Anforderungen ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="c0874-125">This sets the `HttpContext.User` property and runs the Authorization Middleware for requests:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

<span data-ttu-id="c0874-126">Weitere Informationen zu Authentifizierungs Schemas finden Sie unter [Authentifizierungs Konzepte](xref:security/authentication/index#authentication-concepts).</span><span class="sxs-lookup"><span data-stu-id="c0874-126">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="c0874-127">Weitere Informationen zur :::no-loc(cookie)::: Authentifizierung finden Sie unter <xref:security/authentication/:::no-loc(cookie):::> .</span><span class="sxs-lookup"><span data-stu-id="c0874-127">To learn more about :::no-loc(cookie)::: authentication, see <xref:security/authentication/:::no-loc(cookie):::>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="c0874-128">Autorisierung anwenden</span><span class="sxs-lookup"><span data-stu-id="c0874-128">Apply authorization</span></span>

<span data-ttu-id="c0874-129">Testen Sie die Authentifizierungs Konfiguration der APP, indem `AuthorizeAttribute` Sie das-Attribut auf einen Controller, eine Aktion oder eine Seite anwenden.</span><span class="sxs-lookup"><span data-stu-id="c0874-129">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="c0874-130">Der folgende Code schränkt den Zugriff auf die *Datenschutz* Seite auf authentifizierte Benutzer ein:</span><span class="sxs-lookup"><span data-stu-id="c0874-130">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="c0874-131">Abmelden</span><span class="sxs-lookup"><span data-stu-id="c0874-131">Sign out</span></span>

<span data-ttu-id="c0874-132">Um den aktuellen Benutzer zu abmelden und dessen zu löschen, müssen Sie :::no-loc(cookie)::: [signoutasync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)aufrufen.</span><span class="sxs-lookup"><span data-stu-id="c0874-132">To sign out the current user and delete their :::no-loc(cookie):::, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="c0874-133">Der folgende Code fügt `Logout` der *Index* Seite einen Seiten Handler hinzu:</span><span class="sxs-lookup"><span data-stu-id="c0874-133">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="c0874-134">Beachten Sie, dass der-aufrufswert `SignOutAsync` kein Authentifizierungsschema angibt.</span><span class="sxs-lookup"><span data-stu-id="c0874-134">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="c0874-135">Die APP `DefaultScheme` von `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` wird als Fallback verwendet.</span><span class="sxs-lookup"><span data-stu-id="c0874-135">The app's `DefaultScheme` of `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c0874-136">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="c0874-136">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c0874-137"><xref:security/authentication/social/index> Beschreibt, wie Sie es Benutzern ermöglichen können, sich mithilfe von OAuth 2,0 mit Anmelde Informationen von externen Authentifizierungs Anbietern anzumelden.</span><span class="sxs-lookup"><span data-stu-id="c0874-137"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="c0874-138">Die in diesem Thema beschriebene Vorgehensweise umfasst :::no-loc(ASP.NET Core Identity)::: als Authentifizierungs Anbieter.</span><span class="sxs-lookup"><span data-stu-id="c0874-138">The approach described in that topic includes :::no-loc(ASP.NET Core Identity)::: as an authentication provider.</span></span>

<span data-ttu-id="c0874-139">In diesem Beispiel wird veranschaulicht, wie ein externer Authentifizierungs Anbieter **ohne** verwendet wird :::no-loc(ASP.NET Core Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="c0874-139">This sample demonstrates how to use an external authentication provider **without** :::no-loc(ASP.NET Core Identity):::.</span></span> <span data-ttu-id="c0874-140">Dies ist nützlich für apps, die nicht alle Features von benötigen :::no-loc(ASP.NET Core Identity)::: , aber trotzdem eine Integration in einen vertrauenswürdigen externen Authentifizierungs Anbieter erfordern.</span><span class="sxs-lookup"><span data-stu-id="c0874-140">This is useful for apps that don't require all of the features of :::no-loc(ASP.NET Core Identity):::, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="c0874-141">In diesem Beispiel wird die [Google-Authentifizierung](xref:security/authentication/google-logins) zum Authentifizieren von Benutzern verwendet.</span><span class="sxs-lookup"><span data-stu-id="c0874-141">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="c0874-142">Die Verwendung der Google-Authentifizierung verlagert viele der Komplexitäten bei der Verwaltung des Anmeldevorgangs an Google.</span><span class="sxs-lookup"><span data-stu-id="c0874-142">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="c0874-143">Informationen zur Integration in einen anderen externen Authentifizierungs Anbieter finden Sie in den folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="c0874-143">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="c0874-144">Facebook-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="c0874-144">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="c0874-145">Microsoft-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="c0874-145">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="c0874-146">Twitter-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="c0874-146">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="c0874-147">Andere Anbieter</span><span class="sxs-lookup"><span data-stu-id="c0874-147">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="c0874-148">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="c0874-148">Configuration</span></span>

<span data-ttu-id="c0874-149">Konfigurieren Sie in der `ConfigureServices` -Methode die Authentifizierungs Schemas der APP mit den `AddAuthentication` `Add:::no-loc(Cookie):::` Methoden, und `AddGoogle` :</span><span class="sxs-lookup"><span data-stu-id="c0874-149">In the `ConfigureServices` method, configure the app's authentication schemes with the `AddAuthentication`, `Add:::no-loc(Cookie):::`, and `AddGoogle` methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

<span data-ttu-id="c0874-150">Durch den [addauthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) -Rückruf wird das [defaultscheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)der APP festgelegt.</span><span class="sxs-lookup"><span data-stu-id="c0874-150">The call to [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) sets the app's [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme).</span></span> <span data-ttu-id="c0874-151">Das `DefaultScheme` ist das Standardschema, das von den folgenden `HttpContext` Authentifizierungs Erweiterungs Methoden verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="c0874-151">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="c0874-152">Wenn Sie die APP `DefaultScheme` auf [ :::no-loc(Cookie)::: authenticationdefaults. authenticationScheme](xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme) (" :::no-loc(Cookie)::: s") festlegen, wird die APP so konfiguriert, :::no-loc(Cookie)::: dass Sie s als Standardschema für diese Erweiterungs Methoden verwendet.</span><span class="sxs-lookup"><span data-stu-id="c0874-152">Setting the app's `DefaultScheme` to [:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme) (":::no-loc(Cookie):::s") configures the app to use :::no-loc(Cookie):::s as the default scheme for these extension methods.</span></span> <span data-ttu-id="c0874-153">Wenn Sie die APP <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> auf [googledefaults. authenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") festlegen, wird die APP für die Verwendung von Google als Standardschema für Aufrufe von konfiguriert `ChallengeAsync` .</span><span class="sxs-lookup"><span data-stu-id="c0874-153">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="c0874-154">`DefaultChallengeScheme` überschreibt `DefaultScheme` .</span><span class="sxs-lookup"><span data-stu-id="c0874-154">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="c0874-155"><xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>Weitere Eigenschaften, die bei Festlegung überschreiben, finden Sie unter `DefaultScheme` .</span><span class="sxs-lookup"><span data-stu-id="c0874-155">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="c0874-156">Rufen Sie in der- `Configure` Methode die- `UseAuthentication` Methode auf, um die Authentifizierungs Middleware aufzurufen, die die- `HttpContext.User` Eigenschaft festlegt.</span><span class="sxs-lookup"><span data-stu-id="c0874-156">In the `Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="c0874-157">Rufen Sie die-Methode auf, `UseAuthentication` bevor Sie `UseMvcWithDefaultRoute` oder aufrufen `UseMvc` :</span><span class="sxs-lookup"><span data-stu-id="c0874-157">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

<span data-ttu-id="c0874-158">Weitere Informationen zu Authentifizierungs Schemas finden Sie unter [Authentifizierungs Konzepte](xref:security/authentication/index#authentication-concepts).</span><span class="sxs-lookup"><span data-stu-id="c0874-158">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="c0874-159">Weitere Informationen zur :::no-loc(cookie)::: Authentifizierung finden Sie unter <xref:security/authentication/:::no-loc(cookie):::> .</span><span class="sxs-lookup"><span data-stu-id="c0874-159">To learn more about :::no-loc(cookie)::: authentication, see <xref:security/authentication/:::no-loc(cookie):::>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="c0874-160">Autorisierung anwenden</span><span class="sxs-lookup"><span data-stu-id="c0874-160">Apply authorization</span></span>

<span data-ttu-id="c0874-161">Testen Sie die Authentifizierungs Konfiguration der APP, indem `AuthorizeAttribute` Sie das-Attribut auf einen Controller, eine Aktion oder eine Seite anwenden.</span><span class="sxs-lookup"><span data-stu-id="c0874-161">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="c0874-162">Der folgende Code schränkt den Zugriff auf die *Datenschutz* Seite auf authentifizierte Benutzer ein:</span><span class="sxs-lookup"><span data-stu-id="c0874-162">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="c0874-163">Abmelden</span><span class="sxs-lookup"><span data-stu-id="c0874-163">Sign out</span></span>

<span data-ttu-id="c0874-164">Um den aktuellen Benutzer zu abmelden und dessen zu löschen, müssen Sie :::no-loc(cookie)::: [signoutasync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)aufrufen.</span><span class="sxs-lookup"><span data-stu-id="c0874-164">To sign out the current user and delete their :::no-loc(cookie):::, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="c0874-165">Der folgende Code fügt `Logout` der *Index* Seite einen Seiten Handler hinzu:</span><span class="sxs-lookup"><span data-stu-id="c0874-165">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="c0874-166">Beachten Sie, dass der-aufrufswert `SignOutAsync` kein Authentifizierungsschema angibt.</span><span class="sxs-lookup"><span data-stu-id="c0874-166">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="c0874-167">Die APP `DefaultScheme` von `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` wird als Fallback verwendet.</span><span class="sxs-lookup"><span data-stu-id="c0874-167">The app's `DefaultScheme` of `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c0874-168">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="c0874-168">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
