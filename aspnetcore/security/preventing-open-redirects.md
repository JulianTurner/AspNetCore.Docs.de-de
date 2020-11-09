---
title: Verhindern offener Weiterleitungs Angriffe in ASP.net Core
author: ardalis
description: Zeigt, wie Sie Open Redirect-Angriffe gegen eine ASP.net Core-App verhindern können.
ms.author: riande
ms.date: 07/07/2017
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/preventing-open-redirects
ms.openlocfilehash: e546cd852367921c7c694db3639f7a233f606e75
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058388"
---
# <a name="prevent-open-redirect-attacks-in-aspnet-core"></a><span data-ttu-id="25cb8-103">Verhindern offener Weiterleitungs Angriffe in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="25cb8-103">Prevent open redirect attacks in ASP.NET Core</span></span>

<span data-ttu-id="25cb8-104">Eine Web-App, die an eine URL umgeleitet wird, die über die Anforderung angegeben wird, z. b. die QueryString-oder Formulardaten, kann möglicherweise manipuliert werden, um Benutzer an eine externe, böswillige URL umzuleiten.</span><span class="sxs-lookup"><span data-stu-id="25cb8-104">A web app that redirects to a URL that's specified via the request such as the querystring or form data can potentially be tampered with to redirect users to an external, malicious URL.</span></span> <span data-ttu-id="25cb8-105">Diese Manipulation wird als offener Umleitungs Angriff bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="25cb8-105">This tampering is called an open redirection attack.</span></span>

<span data-ttu-id="25cb8-106">Wenn Ihre Anwendungslogik eine Umleitung an eine angegebene URL durchführt, müssen Sie überprüfen, ob die Umleitungs-URL nicht manipuliert wurde.</span><span class="sxs-lookup"><span data-stu-id="25cb8-106">Whenever your application logic redirects to a specified URL, you must verify that the redirection URL hasn't been tampered with.</span></span> <span data-ttu-id="25cb8-107">ASP.net Core verfügt über integrierte Funktionen zum Schutz von apps vor offenen Umleitungs Angriffen (auch als offene Umleitung bezeichnet).</span><span class="sxs-lookup"><span data-stu-id="25cb8-107">ASP.NET Core has built-in functionality to help protect apps from open redirect (also known as open redirection) attacks.</span></span>

## <a name="what-is-an-open-redirect-attack"></a><span data-ttu-id="25cb8-108">Was ist ein offener Umleitungs Angriff?</span><span class="sxs-lookup"><span data-stu-id="25cb8-108">What is an open redirect attack?</span></span>

<span data-ttu-id="25cb8-109">Webanwendungen leiten Benutzer häufig an eine Anmeldeseite weiter, wenn Sie auf Ressourcen zugreifen, die eine Authentifizierung erfordern.</span><span class="sxs-lookup"><span data-stu-id="25cb8-109">Web applications frequently redirect users to a login page when they access resources that require authentication.</span></span> <span data-ttu-id="25cb8-110">Die Umleitung umfasst in der Regel einen `returnUrl` QueryString-Parameter, sodass der Benutzer an die ursprünglich angeforderte URL zurückgegeben werden kann, nachdem Sie sich erfolgreich angemeldet haben.</span><span class="sxs-lookup"><span data-stu-id="25cb8-110">The redirection typically includes a `returnUrl` querystring parameter so that the user can be returned to the originally requested URL after they have successfully logged in.</span></span> <span data-ttu-id="25cb8-111">Nachdem sich der Benutzer authentifiziert hat, werden Sie an die URL umgeleitet, die Sie ursprünglich angefordert hatten.</span><span class="sxs-lookup"><span data-stu-id="25cb8-111">After the user authenticates, they're redirected to the URL they had originally requested.</span></span>

<span data-ttu-id="25cb8-112">Da die Ziel-URL in der Abfrage Zeichenfolge der Anforderung angegeben ist, kann ein böswilliger Benutzer die QueryString manipulieren.</span><span class="sxs-lookup"><span data-stu-id="25cb8-112">Because the destination URL is specified in the querystring of the request, a malicious user could tamper with the querystring.</span></span> <span data-ttu-id="25cb8-113">Eine Manipulation QueryString könnte es der Website ermöglichen, den Benutzer an eine externe, böswillige Website umzuleiten.</span><span class="sxs-lookup"><span data-stu-id="25cb8-113">A tampered querystring could allow the site to redirect the user to an external, malicious site.</span></span> <span data-ttu-id="25cb8-114">Diese Technik wird als offener Umleitungs-oder Umleitungs Angriff bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="25cb8-114">This technique is called an open redirect (or redirection) attack.</span></span>

### <a name="an-example-attack"></a><span data-ttu-id="25cb8-115">Beispiel Angriff</span><span class="sxs-lookup"><span data-stu-id="25cb8-115">An example attack</span></span>

<span data-ttu-id="25cb8-116">Ein böswilliger Benutzer kann einen Angriff entwickeln, der dem böswilligen Benutzer den Zugriff auf die Anmelde Informationen eines Benutzers oder vertrauliche Informationen ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="25cb8-116">A malicious user can develop an attack intended to allow the malicious user access to a user's credentials or sensitive information.</span></span> <span data-ttu-id="25cb8-117">Um mit dem Angriff zu beginnen, überredet der böswillige Benutzer den Benutzer, auf einen Link zur Anmeldeseite Ihrer Website mit einem `returnUrl` QueryString-Wert zu klicken, der der URL hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="25cb8-117">To begin the attack, the malicious user convinces the user to click a link to your site's login page with a `returnUrl` querystring value added to the URL.</span></span> <span data-ttu-id="25cb8-118">Betrachten Sie beispielsweise eine APP, `contoso.com` die eine Anmeldeseite unter enthält `http://contoso.com/Account/LogOn?returnUrl=/Home/About` .</span><span class="sxs-lookup"><span data-stu-id="25cb8-118">For example, consider an app at `contoso.com` that includes a login page at `http://contoso.com/Account/LogOn?returnUrl=/Home/About`.</span></span> <span data-ttu-id="25cb8-119">Der Angriff führt folgende Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="25cb8-119">The attack follows these steps:</span></span>

1. <span data-ttu-id="25cb8-120">Der Benutzer klickt auf einen bösartigen Link zu `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` (die zweite URL lautet "%% **1** amp; quot;", nicht "contoso.com").</span><span class="sxs-lookup"><span data-stu-id="25cb8-120">The user clicks a malicious link to `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` (the second URL is "contoso **1** .com", not "contoso.com").</span></span>
2. <span data-ttu-id="25cb8-121">Der Benutzer meldet sich erfolgreich an.</span><span class="sxs-lookup"><span data-stu-id="25cb8-121">The user logs in successfully.</span></span>
3. <span data-ttu-id="25cb8-122">Der Benutzer wird (von der Site) an `http://contoso1.com/Account/LogOn` (ein böswilliger Standort, der genau wie die tatsächliche Website aussieht) umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="25cb8-122">The user is redirected (by the site) to `http://contoso1.com/Account/LogOn` (a malicious site that looks exactly like real site).</span></span>
4. <span data-ttu-id="25cb8-123">Der Benutzer meldet sich erneut an (gibt die Anmelde Informationen für böswillige Websites an) und wird zurück an die tatsächliche Website weitergeleitet.</span><span class="sxs-lookup"><span data-stu-id="25cb8-123">The user logs in again (giving malicious site their credentials) and is redirected back to the real site.</span></span>

<span data-ttu-id="25cb8-124">Der Benutzer ist wahrscheinlich der Meinung, dass der erste Anmeldeversuch fehlgeschlagen ist und dass der zweite Versuch erfolgreich war.</span><span class="sxs-lookup"><span data-stu-id="25cb8-124">The user likely believes that their first attempt to log in failed and that their second attempt is successful.</span></span> <span data-ttu-id="25cb8-125">Der Benutzer ist wahrscheinlich nicht sicher, dass Ihre Anmelde Informationen kompromittiert sind.</span><span class="sxs-lookup"><span data-stu-id="25cb8-125">The user most likely remains unaware that their credentials are compromised.</span></span>

![Umleitungs Angriffs Prozess öffnen](preventing-open-redirects/_static/open-redirection-attack-process.png)

<span data-ttu-id="25cb8-127">Zusätzlich zu den Anmelde Seiten stellen einige Websites Umleitungs Seiten oder Endpunkte bereit.</span><span class="sxs-lookup"><span data-stu-id="25cb8-127">In addition to login pages, some sites provide redirect pages or endpoints.</span></span> <span data-ttu-id="25cb8-128">Stellen Sie sich vor, Ihre APP verfügt über eine Seite mit einer geöffneten Umleitung, `/Home/Redirect` .</span><span class="sxs-lookup"><span data-stu-id="25cb8-128">Imagine your app has a page with an open redirect, `/Home/Redirect`.</span></span> <span data-ttu-id="25cb8-129">Ein Angreifer könnte z. b. einen Link in einer e-Mail erstellen, die zu gehört `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login` .</span><span class="sxs-lookup"><span data-stu-id="25cb8-129">An attacker could create, for example, a link in an email that goes to `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login`.</span></span> <span data-ttu-id="25cb8-130">Ein typischer Benutzer sieht sich die URL an und zeigt, dass er mit Ihrem Website Namen beginnt.</span><span class="sxs-lookup"><span data-stu-id="25cb8-130">A typical user will look at the URL and see it begins with your site name.</span></span> <span data-ttu-id="25cb8-131">Wenn Sie darauf vertrauen, klicken Sie auf den Link.</span><span class="sxs-lookup"><span data-stu-id="25cb8-131">Trusting that, they will click the link.</span></span> <span data-ttu-id="25cb8-132">Die geöffnete Umleitung würde den Benutzer dann an die Phishingsite senden, die mit Ihrem identisch ist, und der Benutzer würde sich wahrscheinlich bei der Website anmelden, die Sie glauben.</span><span class="sxs-lookup"><span data-stu-id="25cb8-132">The open redirect would then send the user to the phishing site, which looks identical to yours, and the user would likely login to what they believe is your site.</span></span>

## <a name="protecting-against-open-redirect-attacks"></a><span data-ttu-id="25cb8-133">Schutz gegen Open Redirect-Angriffe</span><span class="sxs-lookup"><span data-stu-id="25cb8-133">Protecting against open redirect attacks</span></span>

<span data-ttu-id="25cb8-134">Wenn Sie Webanwendungen entwickeln, behandeln Sie alle vom Benutzer bereitgestellten Daten als nicht vertrauenswürdig.</span><span class="sxs-lookup"><span data-stu-id="25cb8-134">When developing web applications, treat all user-provided data as untrustworthy.</span></span> <span data-ttu-id="25cb8-135">Wenn Ihre Anwendung über Funktionen verfügt, die den Benutzer basierend auf dem Inhalt der URL umleitet, stellen Sie sicher, dass solche Umleitungen nur lokal innerhalb Ihrer APP erfolgen (oder an eine bekannte URL, nicht an eine URL, die in der Abfrage Zeichenfolge angegeben werden kann).</span><span class="sxs-lookup"><span data-stu-id="25cb8-135">If your application has functionality that redirects the user based on the contents of the URL,  ensure that such redirects are only done locally within your app (or to a known URL, not any URL that may be supplied in the querystring).</span></span>

### <a name="localredirect"></a><span data-ttu-id="25cb8-136">Localredirect</span><span class="sxs-lookup"><span data-stu-id="25cb8-136">LocalRedirect</span></span>

<span data-ttu-id="25cb8-137">Verwenden Sie die- `LocalRedirect` Hilfsmethode aus der-Basis `Controller` Klasse:</span><span class="sxs-lookup"><span data-stu-id="25cb8-137">Use the `LocalRedirect` helper method from the base `Controller` class:</span></span>

```csharp
public IActionResult SomeAction(string redirectUrl)
{
    return LocalRedirect(redirectUrl);
}
```

<span data-ttu-id="25cb8-138">`LocalRedirect` löst eine Ausnahme aus, wenn eine nicht lokale URL angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="25cb8-138">`LocalRedirect` will throw an exception if a non-local URL is specified.</span></span> <span data-ttu-id="25cb8-139">Andernfalls verhält sie sich wie die- `Redirect` Methode.</span><span class="sxs-lookup"><span data-stu-id="25cb8-139">Otherwise, it behaves just like the `Redirect` method.</span></span>

### <a name="islocalurl"></a><span data-ttu-id="25cb8-140">Islocalurl</span><span class="sxs-lookup"><span data-stu-id="25cb8-140">IsLocalUrl</span></span>

<span data-ttu-id="25cb8-141">Verwenden Sie die [islocalurl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) -Methode, um URLs vor der Umleitung zu testen:</span><span class="sxs-lookup"><span data-stu-id="25cb8-141">Use the [IsLocalUrl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) method to test URLs before redirecting:</span></span>

<span data-ttu-id="25cb8-142">Im folgenden Beispiel wird gezeigt, wie vor der Umleitung überprüft wird, ob eine URL lokal ist.</span><span class="sxs-lookup"><span data-stu-id="25cb8-142">The following example shows how to check whether a URL is local before redirecting.</span></span>

```csharp
private IActionResult RedirectToLocal(string returnUrl)
{
    if (Url.IsLocalUrl(returnUrl))
    {
        return Redirect(returnUrl);
    }
    else
    {
        return RedirectToAction(nameof(HomeController.Index), "Home");
    }
}
```

<span data-ttu-id="25cb8-143">Mit der- `IsLocalUrl` Methode wird verhindert, dass Benutzer versehentlich an eine böswillige Site umgeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="25cb8-143">The `IsLocalUrl` method protects users from being inadvertently redirected to a malicious site.</span></span> <span data-ttu-id="25cb8-144">Sie können die Details der URL protokollieren, die bereitgestellt wurde, wenn eine nicht lokale URL in einer Situation angegeben wird, in der Sie eine lokale URL erwartet haben.</span><span class="sxs-lookup"><span data-stu-id="25cb8-144">You can log the details of the URL that was provided when a non-local URL is supplied in a situation where you expected a local URL.</span></span> <span data-ttu-id="25cb8-145">Das Protokollieren von Umleitungs-URLs kann bei der Diagnose von Umleitungen helfen.</span><span class="sxs-lookup"><span data-stu-id="25cb8-145">Logging redirect URLs may help in diagnosing redirection attacks.</span></span>
