---
title: Verhindern von Angriffen für Website übergreifende Anforderungs Fälschung (XSRF/CSRF) in ASP.net Core
author: steve-smith
description: Erfahren Sie, wie Sie Angriffe auf Web-Apps verhindern, bei denen eine böswillige Website die Interaktion zwischen einem Client Browser und der APP beeinflussen kann.
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 12/05/2019
no-loc:
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
uid: security/anti-request-forgery
ms.openlocfilehash: 201ffe692c1ded3661a5e1ac566f90b29d61ce9e
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690353"
---
# <a name="prevent-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a><span data-ttu-id="651c6-103">Verhindern von Angriffen für Website übergreifende Anforderungs Fälschung (XSRF/CSRF) in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="651c6-103">Prevent Cross-Site Request Forgery (XSRF/CSRF) attacks in ASP.NET Core</span></span>

<span data-ttu-id="651c6-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT), von " [fyaz Hasan](https://twitter.com/FiyazBinHasan)" und [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="651c6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="651c6-105">Website übergreifende Anforderungs Fälschung (auch als XSRF oder CSRF bezeichnet) ist ein Angriff auf im Web gehostete Apps, bei dem eine böswillige Web-App die Interaktion zwischen einem Client Browser und einer Web-App beeinflussen kann, die diesem Browser vertraut.</span><span class="sxs-lookup"><span data-stu-id="651c6-105">Cross-site request forgery (also known as XSRF or CSRF) is an attack against web-hosted apps whereby a malicious web app can influence the interaction between a client browser and a web app that trusts that browser.</span></span> <span data-ttu-id="651c6-106">Diese Angriffe sind möglich, da Webbrowser einige Arten von Authentifizierungs Token automatisch mit jeder Anforderung an eine Website senden.</span><span class="sxs-lookup"><span data-stu-id="651c6-106">These attacks are possible because web browsers send some types of authentication tokens automatically with every request to a website.</span></span> <span data-ttu-id="651c6-107">Diese Art von Exploit wird auch als *One-Click-Angriff* oder *Sitzungsart* bezeichnet, da der Angriff die zuvor authentifizierte Sitzung des Benutzers nutzt.</span><span class="sxs-lookup"><span data-stu-id="651c6-107">This form of exploit is also known as a *one-click attack* or *session riding* because the attack takes advantage of the user's previously authenticated session.</span></span>

<span data-ttu-id="651c6-108">Ein Beispiel für einen CSRF-Angriff:</span><span class="sxs-lookup"><span data-stu-id="651c6-108">An example of a CSRF attack:</span></span>

1. <span data-ttu-id="651c6-109">Ein Benutzer meldet sich `www.good-banking-site.com` mit der Formular Authentifizierung an.</span><span class="sxs-lookup"><span data-stu-id="651c6-109">A user signs into `www.good-banking-site.com` using forms authentication.</span></span> <span data-ttu-id="651c6-110">Der Server authentifiziert den Benutzer und gibt eine Antwort aus, die eine Authentifizierung enthält :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="651c6-110">The server authenticates the user and issues a response that includes an authentication :::no-loc(cookie):::.</span></span> <span data-ttu-id="651c6-111">Die Site ist anfällig für Angriffe, da Sie jede empfangene Anforderung mit einer gültigen Authentifizierung vertraut :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="651c6-111">The site is vulnerable to attack because it trusts any request that it receives with a valid authentication :::no-loc(cookie):::.</span></span>
1. <span data-ttu-id="651c6-112">Der Benutzer besucht eine böswillige Website, `www.bad-crook-site.com` .</span><span class="sxs-lookup"><span data-stu-id="651c6-112">The user visits a malicious site, `www.bad-crook-site.com`.</span></span>

   <span data-ttu-id="651c6-113">Die böswillige Site `www.bad-crook-site.com` enthält ein HTML-Formular, das dem folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="651c6-113">The malicious site, `www.bad-crook-site.com`, contains an HTML form similar to the following:</span></span>

   ```html
   <h1>Congratulations! You're a Winner!</h1>
   <form action="http://good-banking-site.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw">
       <input type="hidden" name="Amount" value="1000000">
       <input type="submit" value="Click to collect your prize!">
   </form>
   ```

   <span data-ttu-id="651c6-114">Beachten Sie, dass das Formular die `action` Beiträge an die anfällige Site und nicht an die böswillige Site sendet.</span><span class="sxs-lookup"><span data-stu-id="651c6-114">Notice that the form's `action` posts to the vulnerable site, not to the malicious site.</span></span> <span data-ttu-id="651c6-115">Dies ist der "Cross-Site"-Teil von CSRF.</span><span class="sxs-lookup"><span data-stu-id="651c6-115">This is the "cross-site" part of CSRF.</span></span>

1. <span data-ttu-id="651c6-116">Der Benutzer wählt die Schaltfläche Senden aus.</span><span class="sxs-lookup"><span data-stu-id="651c6-116">The user selects the submit button.</span></span> <span data-ttu-id="651c6-117">Der Browser stellt die Anforderung her und schließt automatisch die Authentifizierung :::no-loc(cookie)::: für die angeforderte Domäne ein `www.good-banking-site.com` .</span><span class="sxs-lookup"><span data-stu-id="651c6-117">The browser makes the request and automatically includes the authentication :::no-loc(cookie)::: for the requested domain, `www.good-banking-site.com`.</span></span>
1. <span data-ttu-id="651c6-118">Die Anforderung wird auf dem `www.good-banking-site.com` Server mit dem Authentifizierungs Kontext des Benutzers ausgeführt und kann alle Aktionen ausführen, die ein authentifizierter Benutzer ausführen darf.</span><span class="sxs-lookup"><span data-stu-id="651c6-118">The request runs on the `www.good-banking-site.com` server with the user's authentication context and can perform any action that an authenticated user is allowed to perform.</span></span>

<span data-ttu-id="651c6-119">Zusätzlich zu dem Szenario, in dem der Benutzer die Schaltfläche zum Senden des Formulars auswählt, könnte die böswillige Site folgende Aktionen ausführen:</span><span class="sxs-lookup"><span data-stu-id="651c6-119">In addition to the scenario where the user selects the button to submit the form, the malicious site could:</span></span>

* <span data-ttu-id="651c6-120">Führen Sie ein Skript aus, das das Formular automatisch sendet.</span><span class="sxs-lookup"><span data-stu-id="651c6-120">Run a script that automatically submits the form.</span></span>
* <span data-ttu-id="651c6-121">Senden Sie die Formular Übermittlung als AJAX-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="651c6-121">Send the form submission as an AJAX request.</span></span>
* <span data-ttu-id="651c6-122">Blenden Sie das Formular mit CSS aus.</span><span class="sxs-lookup"><span data-stu-id="651c6-122">Hide the form using CSS.</span></span>

<span data-ttu-id="651c6-123">Für diese alternativen Szenarien sind keine Aktionen oder Eingaben vom Benutzer erforderlich, außer wenn Sie zunächst die böswillige Website besuchen.</span><span class="sxs-lookup"><span data-stu-id="651c6-123">These alternative scenarios don't require any action or input from the user other than initially visiting the malicious site.</span></span>

<span data-ttu-id="651c6-124">Durch die Verwendung von HTTPS wird kein CSRF-Angriff verhindert.</span><span class="sxs-lookup"><span data-stu-id="651c6-124">Using HTTPS doesn't prevent a CSRF attack.</span></span> <span data-ttu-id="651c6-125">Die böswillige Site kann eine `https://www.good-banking-site.com/` Anforderung genauso einfach senden, wie Sie eine unsichere Anforderung senden kann.</span><span class="sxs-lookup"><span data-stu-id="651c6-125">The malicious site can send an `https://www.good-banking-site.com/` request just as easily as it can send an insecure request.</span></span>

<span data-ttu-id="651c6-126">Einige Angriffe zielen auf Endpunkte ab, die auf Get-Anforderungen reagieren. in diesem Fall kann ein imagetag verwendet werden, um die Aktion auszuführen.</span><span class="sxs-lookup"><span data-stu-id="651c6-126">Some attacks target endpoints that respond to GET requests, in which case an image tag can be used to perform the action.</span></span> <span data-ttu-id="651c6-127">Diese Art von Angriff erfolgt häufig auf Forums Websites, die Bilder zulassen, aber JavaScript blockieren.</span><span class="sxs-lookup"><span data-stu-id="651c6-127">This form of attack is common on forum sites that permit images but block JavaScript.</span></span> <span data-ttu-id="651c6-128">Apps, die den Status von Get-Anforderungen ändern, bei denen Variablen oder Ressourcen geändert werden, sind anfällig für böswillige Angriffe.</span><span class="sxs-lookup"><span data-stu-id="651c6-128">Apps that change state on GET requests, where variables or resources are altered, are vulnerable to malicious attacks.</span></span> <span data-ttu-id="651c6-129">**Get-Anforderungen, die den Zustand ändern, sind unsicher. Eine bewährte Vorgehensweise besteht darin, den Status einer GET-Anforderung nie zu ändern.**</span><span class="sxs-lookup"><span data-stu-id="651c6-129">**GET requests that change state are insecure. A best practice is to never change state on a GET request.**</span></span>

<span data-ttu-id="651c6-130">Csrf-Angriffe sind für Web-Apps möglich, die :::no-loc(cookie)::: für die Authentifizierung s verwenden:</span><span class="sxs-lookup"><span data-stu-id="651c6-130">CSRF attacks are possible against web apps that use :::no-loc(cookie):::s for authentication because:</span></span>

* <span data-ttu-id="651c6-131">Browser speichern die :::no-loc(cookie)::: von einer Web-App ausgestellten.</span><span class="sxs-lookup"><span data-stu-id="651c6-131">Browsers store :::no-loc(cookie):::s issued by a web app.</span></span>
* <span data-ttu-id="651c6-132">Gespeicherte :::no-loc(cookie)::: s enthalten Sitzungs- :::no-loc(cookie)::: e für authentifizierte Benutzer.</span><span class="sxs-lookup"><span data-stu-id="651c6-132">Stored :::no-loc(cookie):::s include session :::no-loc(cookie):::s for authenticated users.</span></span>
* <span data-ttu-id="651c6-133">Browser senden alle :::no-loc(cookie)::: e, die einer Domäne zugeordnet sind, an die Web-App jede Anforderung, unabhängig davon, wie die App-Anforderung innerhalb des Browsers generiert wurde.</span><span class="sxs-lookup"><span data-stu-id="651c6-133">Browsers send all of the :::no-loc(cookie):::s associated with a domain to the web app every request regardless of how the request to app was generated within the browser.</span></span>

<span data-ttu-id="651c6-134">Allerdings sind CSRF-Angriffe nicht auf das Ausnutzen von :::no-loc(cookie)::: s beschränkt.</span><span class="sxs-lookup"><span data-stu-id="651c6-134">However, CSRF attacks aren't limited to exploiting :::no-loc(cookie):::s.</span></span> <span data-ttu-id="651c6-135">Beispielsweise sind die Standard-und Digestauthentifizierung auch anfällig.</span><span class="sxs-lookup"><span data-stu-id="651c6-135">For example, Basic and Digest authentication are also vulnerable.</span></span> <span data-ttu-id="651c6-136">Nachdem sich ein Benutzer mit der Standard-oder Digestauthentifizierung angemeldet hat, sendet der Browser die Anmelde Informationen automatisch, bis die Sitzung &dagger; beendet wird.</span><span class="sxs-lookup"><span data-stu-id="651c6-136">After a user signs in with Basic or Digest authentication, the browser automatically sends the credentials until the session&dagger; ends.</span></span>

<span data-ttu-id="651c6-137">&dagger;In diesem Kontext bezieht sich *Sitzung* auf die Client seitige Sitzung, in der der Benutzer authentifiziert wird.</span><span class="sxs-lookup"><span data-stu-id="651c6-137">&dagger;In this context, *session* refers to the client-side session during which the user is authenticated.</span></span> <span data-ttu-id="651c6-138">Sie steht nicht im Zusammenhang mit serverseitigen Sitzungen oder [ASP.net Core Sitzungs Middleware](xref:fundamentals/app-state).</span><span class="sxs-lookup"><span data-stu-id="651c6-138">It's unrelated to server-side sessions or [ASP.NET Core Session Middleware](xref:fundamentals/app-state).</span></span>

<span data-ttu-id="651c6-139">Benutzer können Schutz vor CSRF-Sicherheitsrisiken durch Vorkehrungen treffen:</span><span class="sxs-lookup"><span data-stu-id="651c6-139">Users can guard against CSRF vulnerabilities by taking precautions:</span></span>

* <span data-ttu-id="651c6-140">Melden Sie sich von Web-Apps ab, wenn Sie diese verwenden.</span><span class="sxs-lookup"><span data-stu-id="651c6-140">Sign off of web apps when finished using them.</span></span>
* <span data-ttu-id="651c6-141">Browser :::no-loc(cookie)::: s regelmäßig löschen.</span><span class="sxs-lookup"><span data-stu-id="651c6-141">Clear browser :::no-loc(cookie):::s periodically.</span></span>

<span data-ttu-id="651c6-142">Csrf-Sicherheitsrisiken sind jedoch im Grunde ein Problem mit der Web-App, nicht mit dem Endbenutzer.</span><span class="sxs-lookup"><span data-stu-id="651c6-142">However, CSRF vulnerabilities are fundamentally a problem with the web app, not the end user.</span></span>

## <a name="authentication-fundamentals"></a><span data-ttu-id="651c6-143">Grundlagen der Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="651c6-143">Authentication fundamentals</span></span>

<span data-ttu-id="651c6-144">:::no-loc(Cookie):::die-basierte Authentifizierung ist eine beliebte Form der Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="651c6-144">:::no-loc(Cookie):::-based authentication is a popular form of authentication.</span></span> <span data-ttu-id="651c6-145">Tokenbasierte Authentifizierungssysteme werden immer beliebter, insbesondere für Single-Page-Anwendungen (Spas).</span><span class="sxs-lookup"><span data-stu-id="651c6-145">Token-based authentication systems are growing in popularity, especially for Single Page Applications (SPAs).</span></span>

### <a name="no-loccookie-based-authentication"></a><span data-ttu-id="651c6-146">:::no-loc(Cookie):::-basierte Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="651c6-146">:::no-loc(Cookie):::-based authentication</span></span>

<span data-ttu-id="651c6-147">Wenn ein Benutzer sich mit seinem Benutzernamen und Kennwort authentifiziert, wird ein Token ausgestellt, das ein Authentifizierungs Ticket enthält, das für die Authentifizierung und Autorisierung verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="651c6-147">When a user authenticates using their username and password, they're issued a token, containing an authentication ticket that can be used for authentication and authorization.</span></span> <span data-ttu-id="651c6-148">Das Token wird als gespeichert :::no-loc(cookie)::: , das jede vom Client vornimmt.</span><span class="sxs-lookup"><span data-stu-id="651c6-148">The token is stored as a :::no-loc(cookie)::: that accompanies every request the client makes.</span></span> <span data-ttu-id="651c6-149">Die Erstellung und Überprüfung dieses Vorgangs :::no-loc(cookie)::: erfolgt durch die :::no-loc(Cookie)::: Authentifizierungs Middleware.</span><span class="sxs-lookup"><span data-stu-id="651c6-149">Generating and validating this :::no-loc(cookie)::: is performed by the :::no-loc(Cookie)::: Authentication Middleware.</span></span> <span data-ttu-id="651c6-150">Die [Middleware](xref:fundamentals/middleware/index) serialisiert einen Benutzer Prinzipal in einen verschlüsselten :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="651c6-150">The [middleware](xref:fundamentals/middleware/index) serializes a user principal into an encrypted :::no-loc(cookie):::.</span></span> <span data-ttu-id="651c6-151">Bei nachfolgenden Anforderungen überprüft die Middleware den :::no-loc(cookie)::: , erstellt den Prinzipal neu und weist den Prinzipal der [User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) -Eigenschaft von [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext)zu.</span><span class="sxs-lookup"><span data-stu-id="651c6-151">On subsequent requests, the middleware validates the :::no-loc(cookie):::, recreates the principal, and assigns the principal to the [User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) property of [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext).</span></span>

### <a name="token-based-authentication"></a><span data-ttu-id="651c6-152">Tokenbasierte Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="651c6-152">Token-based authentication</span></span>

<span data-ttu-id="651c6-153">Wenn ein Benutzer authentifiziert wird, wird ein Token ausgestellt (kein antifälschungstoken).</span><span class="sxs-lookup"><span data-stu-id="651c6-153">When a user is authenticated, they're issued a token (not an antiforgery token).</span></span> <span data-ttu-id="651c6-154">Das Token enthält Benutzerinformationen in Form von [Ansprüchen](/dotnet/framework/security/claims-based-identity-model) oder ein Verweis Token, das die APP auf den Benutzer Zustand verweist, der in der APP verwaltet wird.</span><span class="sxs-lookup"><span data-stu-id="651c6-154">The token contains user information in the form of [claims](/dotnet/framework/security/claims-based-identity-model) or a reference token that points the app to user state maintained in the app.</span></span> <span data-ttu-id="651c6-155">Wenn ein Benutzer versucht, auf eine Ressource zuzugreifen, die eine Authentifizierung erfordert, wird das Token mit einem zusätzlichen Autorisierungs Header als bearertoken an die APP gesendet.</span><span class="sxs-lookup"><span data-stu-id="651c6-155">When a user attempts to access a resource requiring authentication, the token is sent to the app with an additional authorization header in form of Bearer token.</span></span> <span data-ttu-id="651c6-156">Dadurch wird die APP zustandslos.</span><span class="sxs-lookup"><span data-stu-id="651c6-156">This makes the app stateless.</span></span> <span data-ttu-id="651c6-157">In jeder nachfolgenden Anforderung wird das Token in der Anforderung für die serverseitige Validierung übermittelt.</span><span class="sxs-lookup"><span data-stu-id="651c6-157">In each subsequent request, the token is passed in the request for server-side validation.</span></span> <span data-ttu-id="651c6-158">Dieses Token ist nicht *verschlüsselt* . Sie ist *codiert* .</span><span class="sxs-lookup"><span data-stu-id="651c6-158">This token isn't *encrypted* ; it's *encoded* .</span></span> <span data-ttu-id="651c6-159">Auf dem Server wird das Token decodiert, um auf seine Informationen zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="651c6-159">On the server, the token is decoded to access its information.</span></span> <span data-ttu-id="651c6-160">Um das Token für nachfolgende Anforderungen zu senden, speichern Sie das Token im lokalen Speicher des Browsers.</span><span class="sxs-lookup"><span data-stu-id="651c6-160">To send the token on subsequent requests, store the token in the browser's local storage.</span></span> <span data-ttu-id="651c6-161">Machen Sie sich keine Gedanken über das Sicherheitsrisiko von CSRF, wenn das Token im lokalen Speicher des Browsers gespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="651c6-161">Don't be concerned about CSRF vulnerability if the token is stored in the browser's local storage.</span></span> <span data-ttu-id="651c6-162">Csrf ist ein Problem, wenn das Token in einer gespeichert wird :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="651c6-162">CSRF is a concern when the token is stored in a :::no-loc(cookie):::.</span></span> <span data-ttu-id="651c6-163">Weitere Informationen finden Sie im GitHub Issue [Spa-Codebeispiel für das Hinzufügen von zwei :::no-loc(cookie)::: s](https://github.com/dotnet/AspNetCore.Docs/issues/13369).</span><span class="sxs-lookup"><span data-stu-id="651c6-163">For more information, see the GitHub issue [SPA code sample adds two :::no-loc(cookie):::s](https://github.com/dotnet/AspNetCore.Docs/issues/13369).</span></span>

### <a name="multiple-apps-hosted-at-one-domain"></a><span data-ttu-id="651c6-164">Mehrere apps, die in einer Domäne gehostet werden</span><span class="sxs-lookup"><span data-stu-id="651c6-164">Multiple apps hosted at one domain</span></span>

<span data-ttu-id="651c6-165">Freigegebene Hostingumgebungen sind anfällig für Session Hijacking, Login CSRF und andere Angriffe.</span><span class="sxs-lookup"><span data-stu-id="651c6-165">Shared hosting environments are vulnerable to session hijacking, login CSRF, and other attacks.</span></span>

<span data-ttu-id="651c6-166">Obwohl `example1.contoso.net` `example2.contoso.net` es sich bei und um unterschiedliche Hosts handelt, gibt es eine implizite Vertrauensstellung zwischen den Hosts in der `*.contoso.net` Domäne.</span><span class="sxs-lookup"><span data-stu-id="651c6-166">Although `example1.contoso.net` and `example2.contoso.net` are different hosts, there's an implicit trust relationship between hosts under the `*.contoso.net` domain.</span></span> <span data-ttu-id="651c6-167">Diese implizite Vertrauensstellung ermöglicht möglicherweise nicht vertrauenswürdige Hosts, sich gegenseitig zu beeinflussen :::no-loc(cookie)::: (die gleichen Ursprungs Richtlinien, die die Regeln von AJAX-Anforderungen Regeln, gelten nicht unbedingt für HTTP :::no-loc(cookie)::: s).</span><span class="sxs-lookup"><span data-stu-id="651c6-167">This implicit trust relationship allows potentially untrusted hosts to affect each other's :::no-loc(cookie):::s (the same-origin policies that govern AJAX requests don't necessarily apply to HTTP :::no-loc(cookie):::s).</span></span>

<span data-ttu-id="651c6-168">Angriffe, die vertrauenswürdige :::no-loc(cookie)::: s zwischen apps ausnutzen, die in derselben Domäne gehostet werden, können durch das Freigeben von Domänen verhindert werden.</span><span class="sxs-lookup"><span data-stu-id="651c6-168">Attacks that exploit trusted :::no-loc(cookie):::s between apps hosted on the same domain can be prevented by not sharing domains.</span></span> <span data-ttu-id="651c6-169">Wenn jede app in ihrer eigenen Domäne gehostet wird, gibt es keine implizite :::no-loc(cookie)::: Vertrauensstellung für die Ausnutzung.</span><span class="sxs-lookup"><span data-stu-id="651c6-169">When each app is hosted on its own domain, there is no implicit :::no-loc(cookie)::: trust relationship to exploit.</span></span>

## <a name="aspnet-core-antiforgery-configuration"></a><span data-ttu-id="651c6-170">Konfiguration der ASP.net Core-Antifälschung</span><span class="sxs-lookup"><span data-stu-id="651c6-170">ASP.NET Core antiforgery configuration</span></span>

> [!WARNING]
> <span data-ttu-id="651c6-171">ASP.net Core implementiert Antifälschung mithilfe [ASP.net Core Datenschutzes](xref:security/data-protection/introduction).</span><span class="sxs-lookup"><span data-stu-id="651c6-171">ASP.NET Core implements antiforgery using [ASP.NET Core Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="651c6-172">Der Stapel für den Datenschutz muss so konfiguriert sein, dass er in einer Serverfarm funktioniert.</span><span class="sxs-lookup"><span data-stu-id="651c6-172">The data protection stack must be configured to work in a server farm.</span></span> <span data-ttu-id="651c6-173">Weitere Informationen finden Sie unter [Konfigurieren des Schutzes von Daten](xref:security/data-protection/configuration/overview) .</span><span class="sxs-lookup"><span data-stu-id="651c6-173">See [Configuring data protection](xref:security/data-protection/configuration/overview) for more information.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="651c6-174">Die Middleware für die Antifälschung wird dem Container für die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) hinzugefügt, wenn eine der folgenden APIs in aufgerufen wird `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="651c6-174">Antiforgery middleware is added to the [Dependency injection](xref:fundamentals/dependency-injection) container when one of the following APIs is called in `Startup.ConfigureServices`:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>
* <xref:Microsoft.AspNetCore.Builder.:::no-loc(Razor):::PagesEndpointRouteBuilderExtensions.Map:::no-loc(Razor):::Pages*>
* <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>
* <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.Map:::no-loc(Blazor):::Hub*>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="651c6-175">Antifälschungsmiddleware wird dem Container für die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) hinzugefügt, wenn <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> in aufgerufen wird. `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="651c6-175">Antiforgery middleware is added to the [Dependency injection](xref:fundamentals/dependency-injection) container when <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> is called in `Startup.ConfigureServices`</span></span>

::: moniker-end

<span data-ttu-id="651c6-176">In ASP.net Core 2,0 oder höher fügt [formtaghelper](xref:mvc/views/working-with-forms#the-form-tag-helper) antifälschungstoken in HTML-Formularelemente ein.</span><span class="sxs-lookup"><span data-stu-id="651c6-176">In ASP.NET Core 2.0 or later, the [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span> <span data-ttu-id="651c6-177">Das folgende Markup in einer :::no-loc(Razor)::: Datei generiert automatisch antifälschungstokentoken:</span><span class="sxs-lookup"><span data-stu-id="651c6-177">The following markup in a :::no-loc(Razor)::: file automatically generates antiforgery tokens:</span></span>

```cshtml
<form method="post">
    ...
</form>
```

<span data-ttu-id="651c6-178">Ebenso generiert [ihtmlhelper. BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) standardmäßig antifälschungstokentoken, wenn die-Methode des Formulars nicht erhalten wird.</span><span class="sxs-lookup"><span data-stu-id="651c6-178">Similarly, [IHtmlHelper.BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) generates antiforgery tokens by default if the form's method isn't GET.</span></span>

<span data-ttu-id="651c6-179">Die automatische Generierung von antifälschungstoken für HTML-Formularelemente tritt auf, wenn das `<form>` -Tag das `method="post"` -Attribut enthält und eine der folgenden Optionen zutrifft:</span><span class="sxs-lookup"><span data-stu-id="651c6-179">The automatic generation of antiforgery tokens for HTML form elements happens when the `<form>` tag contains the `method="post"` attribute and either of the following are true:</span></span>

* <span data-ttu-id="651c6-180">Das Aktions Attribut ist leer ( `action=""` ).</span><span class="sxs-lookup"><span data-stu-id="651c6-180">The action attribute is empty (`action=""`).</span></span>
* <span data-ttu-id="651c6-181">Das action-Attribut wird nicht bereitgestellt ( `<form method="post">` ).</span><span class="sxs-lookup"><span data-stu-id="651c6-181">The action attribute isn't supplied (`<form method="post">`).</span></span>

<span data-ttu-id="651c6-182">Die automatische Generierung von antifälschungstoken für HTML-Formularelemente kann deaktiviert werden:</span><span class="sxs-lookup"><span data-stu-id="651c6-182">Automatic generation of antiforgery tokens for HTML form elements can be disabled:</span></span>

* <span data-ttu-id="651c6-183">Deaktivieren Sie antifälschungstoken explizit mit dem- `asp-antiforgery` Attribut:</span><span class="sxs-lookup"><span data-stu-id="651c6-183">Explicitly disable antiforgery tokens with the `asp-antiforgery` attribute:</span></span>

  ```cshtml
  <form method="post" asp-antiforgery="false">
      ...
  </form>
  ```

* <span data-ttu-id="651c6-184">Das Formular Element wird von taghilfsprogrammen mithilfe des taghilfsprogramms [! Opt-out-Symbols](xref:mvc/views/tag-helpers/intro#opt-out)deaktiviert:</span><span class="sxs-lookup"><span data-stu-id="651c6-184">The form element is opted-out of Tag Helpers by using the Tag Helper [! opt-out symbol](xref:mvc/views/tag-helpers/intro#opt-out):</span></span>

  ```cshtml
  <!form method="post">
      ...
  </!form>
  ```

* <span data-ttu-id="651c6-185">Entfernen Sie `FormTagHelper` aus der Ansicht.</span><span class="sxs-lookup"><span data-stu-id="651c6-185">Remove the `FormTagHelper` from the view.</span></span> <span data-ttu-id="651c6-186">Der `FormTagHelper` kann aus einer Ansicht entfernt werden, indem der Ansicht die folgende-Direktive hinzugefügt wird :::no-loc(Razor)::: :</span><span class="sxs-lookup"><span data-stu-id="651c6-186">The `FormTagHelper` can be removed from a view by adding the following directive to the :::no-loc(Razor)::: view:</span></span>

  ```cshtml
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> <span data-ttu-id="651c6-187">[ :::no-loc(Razor)::: Seiten](xref:razor-pages/index) werden automatisch vor XSRF/CSRF geschützt.</span><span class="sxs-lookup"><span data-stu-id="651c6-187">[:::no-loc(Razor)::: Pages](xref:razor-pages/index) are automatically protected from XSRF/CSRF.</span></span> <span data-ttu-id="651c6-188">Weitere Informationen finden Sie unter [XSRF/CSRF und :::no-loc(Razor)::: Seiten](xref:razor-pages/index#xsrf).</span><span class="sxs-lookup"><span data-stu-id="651c6-188">For more information, see [XSRF/CSRF and :::no-loc(Razor)::: Pages](xref:razor-pages/index#xsrf).</span></span>

<span data-ttu-id="651c6-189">Der häufigste Ansatz für die Verteidigung gegen CSRF-Angriffe ist die Verwendung des *Synchronisierungs Token-Musters* (STP).</span><span class="sxs-lookup"><span data-stu-id="651c6-189">The most common approach to defending against CSRF attacks is to use the *Synchronizer Token Pattern* (STP).</span></span> <span data-ttu-id="651c6-190">STP wird verwendet, wenn der Benutzer eine Seite mit Formulardaten anfordert:</span><span class="sxs-lookup"><span data-stu-id="651c6-190">STP is used when the user requests a page with form data:</span></span>

1. <span data-ttu-id="651c6-191">Der Server sendet ein Token, das der Identität des aktuellen Benutzers zugeordnet ist, an den Client.</span><span class="sxs-lookup"><span data-stu-id="651c6-191">The server sends a token associated with the current user's identity to the client.</span></span>
1. <span data-ttu-id="651c6-192">Der Client sendet das Token zur Überprüfung an den Server zurück.</span><span class="sxs-lookup"><span data-stu-id="651c6-192">The client sends back the token to the server for verification.</span></span>
1. <span data-ttu-id="651c6-193">Wenn der Server ein Token empfängt, das nicht mit der Identität des authentifizierten Benutzers identisch ist, wird die Anforderung abgelehnt.</span><span class="sxs-lookup"><span data-stu-id="651c6-193">If the server receives a token that doesn't match the authenticated user's identity, the request is rejected.</span></span>

<span data-ttu-id="651c6-194">Das Token ist eindeutig und unvorhersehbar.</span><span class="sxs-lookup"><span data-stu-id="651c6-194">The token is unique and unpredictable.</span></span> <span data-ttu-id="651c6-195">Das Token kann auch verwendet werden, um eine ordnungsgemäße Sequenzierung einer Reihe von Anforderungen sicherzustellen (z. b. zur Sicherstellung der Anforderungs Sequenz von: Seite 1 > Seite 2 > Seite 3).</span><span class="sxs-lookup"><span data-stu-id="651c6-195">The token can also be used to ensure proper sequencing of a series of requests (for example, ensuring the request sequence of: page 1 > page 2 > page 3).</span></span> <span data-ttu-id="651c6-196">Alle Formulare in ASP.net Core MVC-und :::no-loc(Razor)::: Seitenvorlagen generieren antifälschungstokentoken.</span><span class="sxs-lookup"><span data-stu-id="651c6-196">All of the forms in ASP.NET Core MVC and :::no-loc(Razor)::: Pages templates generate antiforgery tokens.</span></span> <span data-ttu-id="651c6-197">Das folgende Paar von Sicht Beispielen generiert antifälschungstokentoken:</span><span class="sxs-lookup"><span data-stu-id="651c6-197">The following pair of view examples generate antiforgery tokens:</span></span>

```cshtml
<form asp-controller="Manage" asp-action="ChangePassword" method="post">
    ...
</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    ...
}
```

<span data-ttu-id="651c6-198">Fügen Sie einem-Element explizit ein antifälschungstoken hinzu, `<form>` ohne taghilfsprogramme mit der HTML-Hilfe zu verwenden [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken) :</span><span class="sxs-lookup"><span data-stu-id="651c6-198">Explicitly add an antiforgery token to a `<form>` element without using Tag Helpers with the HTML helper [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken):</span></span>

```cshtml
<form action="/" method="post">
    @Html.AntiForgeryToken()
</form>
```

<span data-ttu-id="651c6-199">In jedem der vorangegangenen Fälle fügt ASP.net Core ein ausgeblendetes Formularfeld ähnlich dem folgenden hinzu:</span><span class="sxs-lookup"><span data-stu-id="651c6-199">In each of the preceding cases, ASP.NET Core adds a hidden form field similar to the following:</span></span>

```cshtml
<input name="__RequestVerificationToken" type="hidden" value="CfDJ8NrAkS ... s2-m9Yw">
```

<span data-ttu-id="651c6-200">ASP.net Core enthält drei [Filter](xref:mvc/controllers/filters) zum Arbeiten mit antifälschungstoken:</span><span class="sxs-lookup"><span data-stu-id="651c6-200">ASP.NET Core includes three [filters](xref:mvc/controllers/filters) for working with antiforgery tokens:</span></span>

* [<span data-ttu-id="651c6-201">ValidateAntiForgeryToken</span><span class="sxs-lookup"><span data-stu-id="651c6-201">ValidateAntiForgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)
* [<span data-ttu-id="651c6-202">Autovalidateantiforgerytoken</span><span class="sxs-lookup"><span data-stu-id="651c6-202">AutoValidateAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)
* [<span data-ttu-id="651c6-203">Ignoreantiforgerytoken</span><span class="sxs-lookup"><span data-stu-id="651c6-203">IgnoreAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)

## <a name="antiforgery-options"></a><span data-ttu-id="651c6-204">Antifälschungsoptionen</span><span class="sxs-lookup"><span data-stu-id="651c6-204">Antiforgery options</span></span>

<span data-ttu-id="651c6-205">Optionen für die [Antifälschung](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) anpassen in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="651c6-205">Customize [antiforgery options](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    // Set :::no-loc(Cookie)::: properties using :::no-loc(Cookie):::Builder properties†.
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.SuppressXFrameOptionsHeader = false;
});
```

<span data-ttu-id="651c6-206">&dagger;Legen Sie die Eigenschaften für die Antifälschung `:::no-loc(Cookie):::` mithilfe der Eigenschaften der [ :::no-loc(Cookie)::: Builder](/dotnet/api/microsoft.aspnetcore.http.:::no-loc(cookie):::builder) -Klasse fest.</span><span class="sxs-lookup"><span data-stu-id="651c6-206">&dagger;Set the antiforgery `:::no-loc(Cookie):::` properties using the properties of the [:::no-loc(Cookie):::Builder](/dotnet/api/microsoft.aspnetcore.http.:::no-loc(cookie):::builder) class.</span></span>

| <span data-ttu-id="651c6-207">Option</span><span class="sxs-lookup"><span data-stu-id="651c6-207">Option</span></span> | <span data-ttu-id="651c6-208">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="651c6-208">Description</span></span> |
| ------ | ----------- |
| [:::no-loc(Cookie):::](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.:::no-loc(cookie):::) | <span data-ttu-id="651c6-209">Legt die Einstellungen fest, die zum Erstellen der Antifälschung s verwendet werden :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="651c6-209">Determines the settings used to create the antiforgery :::no-loc(cookie):::s.</span></span> |
| [<span data-ttu-id="651c6-210">FormFieldName</span><span class="sxs-lookup"><span data-stu-id="651c6-210">FormFieldName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | <span data-ttu-id="651c6-211">Der Name des ausgeblendeten Formular Felds, das vom antifälschungs System zum Rendering von antifälschungstoken in Sichten verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="651c6-211">The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> |
| [<span data-ttu-id="651c6-212">Header Name</span><span class="sxs-lookup"><span data-stu-id="651c6-212">HeaderName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | <span data-ttu-id="651c6-213">Der Name des Headers, der vom antifälschungs System verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="651c6-213">The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="651c6-214">Gibt an `null` , dass das System nur Formulardaten berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="651c6-214">If `null`, the system considers only form data.</span></span> |
| [<span data-ttu-id="651c6-215">Suppressxframeoptionsheader</span><span class="sxs-lookup"><span data-stu-id="651c6-215">SuppressXFrameOptionsHeader</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | <span data-ttu-id="651c6-216">Gibt an, ob die Generierung des Headers unterdrückt werden soll `X-Frame-Options` .</span><span class="sxs-lookup"><span data-stu-id="651c6-216">Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="651c6-217">Standardmäßig wird der-Header mit dem Wert "sameorigin" generiert.</span><span class="sxs-lookup"><span data-stu-id="651c6-217">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="651c6-218">Wird standardmäßig auf `false` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="651c6-218">Defaults to `false`.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    options.:::no-loc(Cookie):::Domain = "contoso.com";
    options.:::no-loc(Cookie):::Name = "X-CSRF-TOKEN-COOKIENAME";
    options.:::no-loc(Cookie):::Path = "Path";
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.RequireSsl = false;
    options.SuppressXFrameOptionsHeader = false;
});
```

| <span data-ttu-id="651c6-219">Option</span><span class="sxs-lookup"><span data-stu-id="651c6-219">Option</span></span> | <span data-ttu-id="651c6-220">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="651c6-220">Description</span></span> |
| ------ | ----------- |
| [:::no-loc(Cookie):::](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.:::no-loc(cookie):::) | <span data-ttu-id="651c6-221">Legt die Einstellungen fest, die zum Erstellen der Antifälschung s verwendet werden :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="651c6-221">Determines the settings used to create the antiforgery :::no-loc(cookie):::s.</span></span> |
| <span data-ttu-id="651c6-222">[:::no-loc(Cookie):::Domain](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.:::no-loc(cookie):::domain)</span><span class="sxs-lookup"><span data-stu-id="651c6-222">[:::no-loc(Cookie):::Domain](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.:::no-loc(cookie):::domain)</span></span> | <span data-ttu-id="651c6-223">Die Domäne des :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="651c6-223">The domain of the :::no-loc(cookie):::.</span></span> <span data-ttu-id="651c6-224">Wird standardmäßig auf `null` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="651c6-224">Defaults to `null`.</span></span> <span data-ttu-id="651c6-225">Diese Eigenschaft ist veraltet und wird in einer zukünftigen Version entfernt.</span><span class="sxs-lookup"><span data-stu-id="651c6-225">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="651c6-226">Die empfohlene Alternative ist :::no-loc(Cookie)::: . -.</span><span class="sxs-lookup"><span data-stu-id="651c6-226">The recommended alternative is :::no-loc(Cookie):::.Domain.</span></span> |
| <span data-ttu-id="651c6-227">[:::no-loc(Cookie):::Benennen](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.:::no-loc(cookie):::name)</span><span class="sxs-lookup"><span data-stu-id="651c6-227">[:::no-loc(Cookie):::Name](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.:::no-loc(cookie):::name)</span></span> | <span data-ttu-id="651c6-228">Der Name von :::no-loc(cookie):::.</span><span class="sxs-lookup"><span data-stu-id="651c6-228">The name of the :::no-loc(cookie):::.</span></span> <span data-ttu-id="651c6-229">Wenn nicht festgelegt, generiert das System einen eindeutigen Namen, beginnend mit dem [Standard :::no-loc(Cookie)::: Präfix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.default:::no-loc(cookie):::prefix) (". Aspnetcore. Antifälschung. ").</span><span class="sxs-lookup"><span data-stu-id="651c6-229">If not set, the system generates a unique name beginning with the [Default:::no-loc(Cookie):::Prefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.default:::no-loc(cookie):::prefix) (".AspNetCore.Antiforgery.").</span></span> <span data-ttu-id="651c6-230">Diese Eigenschaft ist veraltet und wird in einer zukünftigen Version entfernt.</span><span class="sxs-lookup"><span data-stu-id="651c6-230">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="651c6-231">Die empfohlene Alternative ist :::no-loc(Cookie)::: . Benennen.</span><span class="sxs-lookup"><span data-stu-id="651c6-231">The recommended alternative is :::no-loc(Cookie):::.Name.</span></span> |
| <span data-ttu-id="651c6-232">[:::no-loc(Cookie):::Pfad](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.:::no-loc(cookie):::path)</span><span class="sxs-lookup"><span data-stu-id="651c6-232">[:::no-loc(Cookie):::Path](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.:::no-loc(cookie):::path)</span></span> | <span data-ttu-id="651c6-233">Der Pfad, der auf dem festgelegt ist :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="651c6-233">The path set on the :::no-loc(cookie):::.</span></span> <span data-ttu-id="651c6-234">Diese Eigenschaft ist veraltet und wird in einer zukünftigen Version entfernt.</span><span class="sxs-lookup"><span data-stu-id="651c6-234">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="651c6-235">Die empfohlene Alternative ist :::no-loc(Cookie)::: . ADS.</span><span class="sxs-lookup"><span data-stu-id="651c6-235">The recommended alternative is :::no-loc(Cookie):::.Path.</span></span> |
| [<span data-ttu-id="651c6-236">FormFieldName</span><span class="sxs-lookup"><span data-stu-id="651c6-236">FormFieldName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | <span data-ttu-id="651c6-237">Der Name des ausgeblendeten Formular Felds, das vom antifälschungs System zum Rendering von antifälschungstoken in Sichten verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="651c6-237">The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> |
| [<span data-ttu-id="651c6-238">Header Name</span><span class="sxs-lookup"><span data-stu-id="651c6-238">HeaderName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | <span data-ttu-id="651c6-239">Der Name des Headers, der vom antifälschungs System verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="651c6-239">The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="651c6-240">Gibt an `null` , dass das System nur Formulardaten berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="651c6-240">If `null`, the system considers only form data.</span></span> |
| [<span data-ttu-id="651c6-241">RequireSsl</span><span class="sxs-lookup"><span data-stu-id="651c6-241">RequireSsl</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | <span data-ttu-id="651c6-242">Gibt an, ob HTTPS für das antifälschungs System erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="651c6-242">Specifies whether HTTPS is required by the antiforgery system.</span></span> <span data-ttu-id="651c6-243">Gibt an `true` , dass nicht-HTTPS-Anforderungen fehlschlagen.</span><span class="sxs-lookup"><span data-stu-id="651c6-243">If `true`, non-HTTPS requests fail.</span></span> <span data-ttu-id="651c6-244">Wird standardmäßig auf `false` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="651c6-244">Defaults to `false`.</span></span> <span data-ttu-id="651c6-245">Diese Eigenschaft ist veraltet und wird in einer zukünftigen Version entfernt.</span><span class="sxs-lookup"><span data-stu-id="651c6-245">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="651c6-246">Die empfohlene Alternative besteht darin, festzulegen :::no-loc(Cookie)::: . Securepolicy.</span><span class="sxs-lookup"><span data-stu-id="651c6-246">The recommended alternative is to set :::no-loc(Cookie):::.SecurePolicy.</span></span> |
| [<span data-ttu-id="651c6-247">Suppressxframeoptionsheader</span><span class="sxs-lookup"><span data-stu-id="651c6-247">SuppressXFrameOptionsHeader</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | <span data-ttu-id="651c6-248">Gibt an, ob die Generierung des Headers unterdrückt werden soll `X-Frame-Options` .</span><span class="sxs-lookup"><span data-stu-id="651c6-248">Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="651c6-249">Standardmäßig wird der-Header mit dem Wert "sameorigin" generiert.</span><span class="sxs-lookup"><span data-stu-id="651c6-249">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="651c6-250">Wird standardmäßig auf `false` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="651c6-250">Defaults to `false`.</span></span> |

::: moniker-end

<span data-ttu-id="651c6-251">Weitere Informationen finden Sie unter [ :::no-loc(Cookie)::: authenticationoptions](/dotnet/api/Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::AuthenticationOptions).</span><span class="sxs-lookup"><span data-stu-id="651c6-251">For more information, see [:::no-loc(Cookie):::AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::AuthenticationOptions).</span></span>

## <a name="configure-antiforgery-features-with-iantiforgery"></a><span data-ttu-id="651c6-252">Konfigurieren von antifälschungsfeatures mit iantifälschung</span><span class="sxs-lookup"><span data-stu-id="651c6-252">Configure antiforgery features with IAntiforgery</span></span>

<span data-ttu-id="651c6-253">[Iantifälschung](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) bietet die API zum Konfigurieren von antifälschungs Features.</span><span class="sxs-lookup"><span data-stu-id="651c6-253">[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) provides the API to configure antiforgery features.</span></span> <span data-ttu-id="651c6-254">`IAntiforgery` kann in der- `Configure` Methode der-Klasse angefordert werden `Startup` .</span><span class="sxs-lookup"><span data-stu-id="651c6-254">`IAntiforgery` can be requested in the `Configure` method of the `Startup` class.</span></span> <span data-ttu-id="651c6-255">Im folgenden Beispiel wird die Middleware auf der Startseite der APP verwendet, um ein antifälschungstoken zu generieren und in der Antwort als zu senden :::no-loc(cookie)::: (mit der standardmäßigen Angular-Benennungs Konvention, die weiter unten in diesem Thema beschrieben wird):</span><span class="sxs-lookup"><span data-stu-id="651c6-255">The following example uses middleware from the app's home page to generate an antiforgery token and send it in the response as a :::no-loc(cookie)::: (using the default Angular naming convention described later in this topic):</span></span>

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable :::no-loc(cookie):::, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.:::no-loc(Cookie):::s.Append("XSRF-TOKEN", tokens.RequestToken, 
                new :::no-loc(Cookie):::Options() { HttpOnly = false });
        }

        return next(context);
    });
}
```

### <a name="require-antiforgery-validation"></a><span data-ttu-id="651c6-256">Antifälschungs Überprüfung erforderlich</span><span class="sxs-lookup"><span data-stu-id="651c6-256">Require antiforgery validation</span></span>

<span data-ttu-id="651c6-257">[Validateantiforgerytoken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) ist ein Aktionsfilter, der auf eine einzelne Aktion, einen Controller oder Global angewendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="651c6-257">[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) is an action filter that can be applied to an individual action, a controller, or globally.</span></span> <span data-ttu-id="651c6-258">Anforderungen an Aktionen, die diesen Filter angewendet haben, werden blockiert, es sei denn, die Anforderung enthält ein gültiges antifälschungstoken.</span><span class="sxs-lookup"><span data-stu-id="651c6-258">Requests made to actions that have this filter applied are blocked unless the request includes a valid antiforgery token.</span></span>

```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> RemoveLogin(RemoveLoginViewModel account)
{
    ManageMessageId? message = ManageMessageId.Error;
    var user = await GetCurrentUserAsync();

    if (user != null)
    {
        var result = 
            await _userManager.RemoveLoginAsync(
                user, account.LoginProvider, account.ProviderKey);

        if (result.Succeeded)
        {
            await _signInManager.SignInAsync(user, isPersistent: false);
            message = ManageMessageId.RemoveLoginSuccess;
        }
    }

    return RedirectToAction(nameof(ManageLogins), new { Message = message });
}
```

<span data-ttu-id="651c6-259">Das- `ValidateAntiForgeryToken` Attribut erfordert ein Token für Anforderungen an die Aktionsmethoden, die es markiert, einschließlich HTTP GET-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="651c6-259">The `ValidateAntiForgeryToken` attribute requires a token for requests to the action methods it marks, including HTTP GET requests.</span></span> <span data-ttu-id="651c6-260">Wenn das `ValidateAntiForgeryToken` -Attribut auf die Controller der APP angewendet wird, kann es mit dem-Attribut überschrieben werden `IgnoreAntiforgeryToken` .</span><span class="sxs-lookup"><span data-stu-id="651c6-260">If the `ValidateAntiForgeryToken` attribute is applied across the app's controllers, it can be overridden with the `IgnoreAntiforgeryToken` attribute.</span></span>

> [!NOTE]
> <span data-ttu-id="651c6-261">ASP.net Core unterstützt das Hinzufügen von antifälschungstoken, um Anforderungen automatisch zu erhalten</span><span class="sxs-lookup"><span data-stu-id="651c6-261">ASP.NET Core doesn't support adding antiforgery tokens to GET requests automatically.</span></span>

### <a name="automatically-validate-antiforgery-tokens-for-unsafe-http-methods-only"></a><span data-ttu-id="651c6-262">Antifälschungstokentoken nur für unsichere HTTP-Methoden automatisch überprüfen</span><span class="sxs-lookup"><span data-stu-id="651c6-262">Automatically validate antiforgery tokens for unsafe HTTP methods only</span></span>

<span data-ttu-id="651c6-263">ASP.net Core apps generieren keine antifälschungstokentoken für sichere HTTP-Methoden (Get, Head, Options und Trace).</span><span class="sxs-lookup"><span data-stu-id="651c6-263">ASP.NET Core apps don't generate antiforgery tokens for safe HTTP methods (GET, HEAD, OPTIONS, and TRACE).</span></span> <span data-ttu-id="651c6-264">Anstatt das Attribut weitgehend anzuwenden `ValidateAntiForgeryToken` und dann mit Attributen zu überschreiben `IgnoreAntiforgeryToken` , kann das [autovalidateantiforgerytoken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) -Attribut verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="651c6-264">Instead of broadly applying the `ValidateAntiForgeryToken` attribute and then overriding it with `IgnoreAntiforgeryToken` attributes, the [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) attribute can be used.</span></span> <span data-ttu-id="651c6-265">Dieses Attribut funktioniert identisch mit dem- `ValidateAntiForgeryToken` Attribut, mit dem Unterschied, dass es keine Token für Anforderungen erfordert, die mit den folgenden HTTP-Methoden ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="651c6-265">This attribute works identically to the `ValidateAntiForgeryToken` attribute, except that it doesn't require tokens for requests made using the following HTTP methods:</span></span>

* <span data-ttu-id="651c6-266">GET</span><span class="sxs-lookup"><span data-stu-id="651c6-266">GET</span></span>
* <span data-ttu-id="651c6-267">HEAD</span><span class="sxs-lookup"><span data-stu-id="651c6-267">HEAD</span></span>
* <span data-ttu-id="651c6-268">OPTIONS</span><span class="sxs-lookup"><span data-stu-id="651c6-268">OPTIONS</span></span>
* <span data-ttu-id="651c6-269">TRACE</span><span class="sxs-lookup"><span data-stu-id="651c6-269">TRACE</span></span>

<span data-ttu-id="651c6-270">Wir empfehlen die Verwendung von `AutoValidateAntiforgeryToken` für nicht-API-Szenarien.</span><span class="sxs-lookup"><span data-stu-id="651c6-270">We recommend use of `AutoValidateAntiforgeryToken` broadly for non-API scenarios.</span></span> <span data-ttu-id="651c6-271">Dadurch wird sichergestellt, dass Post-Aktionen standardmäßig geschützt werden.</span><span class="sxs-lookup"><span data-stu-id="651c6-271">This ensures POST actions are protected by default.</span></span> <span data-ttu-id="651c6-272">Die Alternative besteht darin, antifälschungstokentoken standardmäßig zu ignorieren, es sei denn `ValidateAntiForgeryToken` , wird auf einzelne Aktionsmethoden angewendet.</span><span class="sxs-lookup"><span data-stu-id="651c6-272">The alternative is to ignore antiforgery tokens by default, unless `ValidateAntiForgeryToken` is applied to individual action methods.</span></span> <span data-ttu-id="651c6-273">In diesem Szenario ist es wahrscheinlicher, dass eine Post-Aktionsmethode versehentlich ungeschützt bleibt, und die APP ist anfällig für CSRF-Angriffe.</span><span class="sxs-lookup"><span data-stu-id="651c6-273">It's more likely in this scenario for a POST action method to be left unprotected by mistake, leaving the app vulnerable to CSRF attacks.</span></span> <span data-ttu-id="651c6-274">Alle Beiträge sollten das antifälschungstoken senden.</span><span class="sxs-lookup"><span data-stu-id="651c6-274">All POSTs should send the antiforgery token.</span></span>

<span data-ttu-id="651c6-275">APIs verfügen nicht über einen automatischen Mechanismus zum Senden des nicht- :::no-loc(cookie)::: Teils des Tokens.</span><span class="sxs-lookup"><span data-stu-id="651c6-275">APIs don't have an automatic mechanism for sending the non-:::no-loc(cookie)::: part of the token.</span></span> <span data-ttu-id="651c6-276">Die Implementierung hängt wahrscheinlich von der Implementierung des Client Codes ab.</span><span class="sxs-lookup"><span data-stu-id="651c6-276">The implementation probably depends on the client code implementation.</span></span> <span data-ttu-id="651c6-277">Einige Beispiele sind unten dargestellt:</span><span class="sxs-lookup"><span data-stu-id="651c6-277">Some examples are shown below:</span></span>

<span data-ttu-id="651c6-278">Beispiel auf Klassenebene:</span><span class="sxs-lookup"><span data-stu-id="651c6-278">Class-level example:</span></span>

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
```

<span data-ttu-id="651c6-279">Globales Beispiel:</span><span class="sxs-lookup"><span data-stu-id="651c6-279">Global example:</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="651c6-280">Betreuung. Addmvc (Optionen => Optionen. Filters. Add (neues autovalidateantiforgerytokenattribute ()));</span><span class="sxs-lookup"><span data-stu-id="651c6-280">services.AddMvc(options =>     options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

```csharp
services.AddControllersWithViews(options =>
    options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));
```

::: moniker-end

### <a name="override-global-or-controller-antiforgery-attributes"></a><span data-ttu-id="651c6-281">Globale oder Controller-antifälschungsattribute überschreiben</span><span class="sxs-lookup"><span data-stu-id="651c6-281">Override global or controller antiforgery attributes</span></span>

<span data-ttu-id="651c6-282">Der [ignoreantiforgerytoken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) -Filter wird verwendet, um den Bedarf an einem antifälschungstoken für eine bestimmte Aktion (oder einen Controller) auszuschließen.</span><span class="sxs-lookup"><span data-stu-id="651c6-282">The [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) filter is used to eliminate the need for an antiforgery token for a given action (or controller).</span></span> <span data-ttu-id="651c6-283">Wenn dieser Filter angewendet wird, überschreibt dieser Filter die `ValidateAntiForgeryToken` Filter und Filter, die `AutoValidateAntiforgeryToken` auf einer höheren Ebene (Global oder auf einem Controller) angegeben sind.</span><span class="sxs-lookup"><span data-stu-id="651c6-283">When applied, this filter overrides `ValidateAntiForgeryToken` and `AutoValidateAntiforgeryToken` filters specified at a higher level (globally or on a controller).</span></span>

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
    [HttpPost]
    [IgnoreAntiforgeryToken]
    public async Task<IActionResult> DoSomethingSafe(SomeViewModel model)
    {
        // no antiforgery token required
    }
}
```

## <a name="refresh-tokens-after-authentication"></a><span data-ttu-id="651c6-284">Token nach der Authentifizierung aktualisieren</span><span class="sxs-lookup"><span data-stu-id="651c6-284">Refresh tokens after authentication</span></span>

<span data-ttu-id="651c6-285">Token sollten aktualisiert werden, nachdem der Benutzer authentifiziert wurde, indem er den Benutzer auf eine Seite oder eine :::no-loc(Razor)::: Seiten Seite umleitet.</span><span class="sxs-lookup"><span data-stu-id="651c6-285">Tokens should be refreshed after the user is authenticated by redirecting the user to a view or :::no-loc(Razor)::: Pages page.</span></span>

## <a name="javascript-ajax-and-spas"></a><span data-ttu-id="651c6-286">JavaScript, AJAX und Spas</span><span class="sxs-lookup"><span data-stu-id="651c6-286">JavaScript, AJAX, and SPAs</span></span>

<span data-ttu-id="651c6-287">In herkömmlichen HTML-basierten apps werden antifälschungstokentoken mithilfe von ausgeblendeten Formularfeldern an den Server übermittelt.</span><span class="sxs-lookup"><span data-stu-id="651c6-287">In traditional HTML-based apps, antiforgery tokens are passed to the server using hidden form fields.</span></span> <span data-ttu-id="651c6-288">In modernen JavaScript-basierten apps und Spas werden viele Anforderungen Programm gesteuert durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="651c6-288">In modern JavaScript-based apps and SPAs, many requests are made programmatically.</span></span> <span data-ttu-id="651c6-289">Diese AJAX-Anforderungen können andere Techniken (z. b. Anforderungs Header oder :::no-loc(cookie)::: e) verwenden, um das Token zu senden.</span><span class="sxs-lookup"><span data-stu-id="651c6-289">These AJAX requests may use other techniques (such as request headers or :::no-loc(cookie):::s) to send the token.</span></span>

<span data-ttu-id="651c6-290">Wenn :::no-loc(cookie)::: e zum Speichern von Authentifizierungs Token und zum Authentifizieren von API-Anforderungen auf dem Server verwendet werden, ist CSRF ein potenzielles Problem.</span><span class="sxs-lookup"><span data-stu-id="651c6-290">If :::no-loc(cookie):::s are used to store authentication tokens and to authenticate API requests on the server, CSRF is a potential problem.</span></span> <span data-ttu-id="651c6-291">Wenn lokaler Speicher zum Speichern des Tokens verwendet wird, wird die CSRF-Sicherheits Anfälligkeit möglicherweise verringert, da Werte aus lokalem Speicher nicht automatisch mit jeder Anforderung an den Server gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="651c6-291">If local storage is used to store the token, CSRF vulnerability might be mitigated because values from local storage aren't sent automatically to the server with every request.</span></span> <span data-ttu-id="651c6-292">Daher ist die Verwendung von lokalem Speicher, um das antifälschungstoken auf dem Client zu speichern und das Token als Anforderungs Header zu senden, eine empfohlene Vorgehensweise.</span><span class="sxs-lookup"><span data-stu-id="651c6-292">Thus, using local storage to store the antiforgery token on the client and sending the token as a request header is a recommended approach.</span></span>

### <a name="javascript"></a><span data-ttu-id="651c6-293">JavaScript</span><span class="sxs-lookup"><span data-stu-id="651c6-293">JavaScript</span></span>

<span data-ttu-id="651c6-294">Mithilfe von JavaScript mit Sichten kann das Token mithilfe eines Diensts innerhalb der Sicht erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="651c6-294">Using JavaScript with views, the token can be created using a service from within the view.</span></span> <span data-ttu-id="651c6-295">Fügen Sie den Dienst " [Microsoft. aspnetcore. Antifälschung. iantifälschung](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) " in die Ansicht ein, und nennen Sie " [getandstoretokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens)":</span><span class="sxs-lookup"><span data-stu-id="651c6-295">Inject the [Microsoft.AspNetCore.Antiforgery.IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) service into the view and call [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):</span></span>

[!code-cshtml[](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,12-13,35-36)]

<span data-ttu-id="651c6-296">Bei dieser Vorgehensweise entfällt die Notwendigkeit, direkt mit dem Einrichten :::no-loc(cookie)::: von s vom Server oder dem Lesen aus dem Client umzugehen.</span><span class="sxs-lookup"><span data-stu-id="651c6-296">This approach eliminates the need to deal directly with setting :::no-loc(cookie):::s from the server or reading them from the client.</span></span>

<span data-ttu-id="651c6-297">Im vorangehenden Beispiel wird JavaScript verwendet, um den Wert des ausgeblendeten Felds für den AJAX-Post-Header</span><span class="sxs-lookup"><span data-stu-id="651c6-297">The preceding example uses JavaScript to read the hidden field value for the AJAX POST header.</span></span>

<span data-ttu-id="651c6-298">JavaScript kann auch auf Token in :::no-loc(cookie)::: s zugreifen und den Inhalt des s verwenden :::no-loc(cookie)::: , um einen Header mit dem Wert des Tokens zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="651c6-298">JavaScript can also access tokens in :::no-loc(cookie):::s and use the :::no-loc(cookie):::'s contents to create a header with the token's value.</span></span>

```csharp
context.Response.:::no-loc(Cookie):::s.Append("CSRF-TOKEN", tokens.RequestToken, 
    new Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Options { HttpOnly = false });
```

<span data-ttu-id="651c6-299">Wenn das Skript anfordert, das Token in einem Header mit dem Namen zu senden `X-CSRF-TOKEN` , konfigurieren Sie den antifälschungs Dienst so, dass er nach dem `X-CSRF-TOKEN` Header sucht:</span><span class="sxs-lookup"><span data-stu-id="651c6-299">Assuming the script requests to send the token in a header called `X-CSRF-TOKEN`, configure the antiforgery service to look for the `X-CSRF-TOKEN` header:</span></span>

```csharp
services.AddAntiforgery(options => options.HeaderName = "X-CSRF-TOKEN");
```

<span data-ttu-id="651c6-300">Im folgenden Beispiel wird JavaScript verwendet, um eine AJAX-Anforderung mit dem entsprechenden Header zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="651c6-300">The following example uses JavaScript to make an AJAX request with the appropriate header:</span></span>

```javascript
function get:::no-loc(Cookie):::(cname) {
    var name = cname + "=";
    var decoded:::no-loc(Cookie)::: = decodeURIComponent(document.:::no-loc(cookie):::);
    var ca = decoded:::no-loc(Cookie):::.split(';');
    for(var i = 0; i <ca.length; i++) {
        var c = ca[i];
        while (c.charAt(0) == ' ') {
            c = c.substring(1);
        }
        if (c.indexOf(name) == 0) {
            return c.substring(name.length, c.length);
        }
    }
    return "";
}

var csrfToken = get:::no-loc(Cookie):::("CSRF-TOKEN");

var xhttp = new XMLHttpRequest();
xhttp.onreadystatechange = function() {
    if (xhttp.readyState == XMLHttpRequest.DONE) {
        if (xhttp.status == 200) {
            alert(xhttp.responseText);
        } else {
            alert('There was an error processing the AJAX request.');
        }
    }
};
xhttp.open('POST', '/api/password/changepassword', true);
xhttp.setRequestHeader("Content-type", "application/json");
xhttp.setRequestHeader("X-CSRF-TOKEN", csrfToken);
xhttp.send(JSON.stringify({ "newPassword": "ReallySecurePassword999$$$" }));
```

### <a name="angularjs"></a><span data-ttu-id="651c6-301">AngularJS</span><span class="sxs-lookup"><span data-stu-id="651c6-301">AngularJS</span></span>

<span data-ttu-id="651c6-302">Angularjs verwendet eine Konvention, um CSRF zu adressieren.</span><span class="sxs-lookup"><span data-stu-id="651c6-302">AngularJS uses a convention to address CSRF.</span></span> <span data-ttu-id="651c6-303">Wenn der Server einen :::no-loc(cookie)::: mit dem Namen sendet `XSRF-TOKEN` , fügt der angularjs- `$http` Dienst den :::no-loc(cookie)::: Wert einem Header hinzu, wenn er eine Anforderung an den Server sendet.</span><span class="sxs-lookup"><span data-stu-id="651c6-303">If the server sends a :::no-loc(cookie)::: with the name `XSRF-TOKEN`, the AngularJS `$http` service adds the :::no-loc(cookie)::: value to a header when it sends a request to the server.</span></span> <span data-ttu-id="651c6-304">Dieser Prozess erfolgt automatisch.</span><span class="sxs-lookup"><span data-stu-id="651c6-304">This process is automatic.</span></span> <span data-ttu-id="651c6-305">Der-Header muss nicht explizit im Client festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="651c6-305">The header doesn't need to be set in the client explicitly.</span></span> <span data-ttu-id="651c6-306">Der Header Name ist `X-XSRF-TOKEN` .</span><span class="sxs-lookup"><span data-stu-id="651c6-306">The header name is `X-XSRF-TOKEN`.</span></span> <span data-ttu-id="651c6-307">Der Server sollte diesen Header erkennen und seinen Inhalt überprüfen.</span><span class="sxs-lookup"><span data-stu-id="651c6-307">The server should detect this header and validate its contents.</span></span>

<span data-ttu-id="651c6-308">Damit ASP.net Core-API mit dieser Konvention beim Starten der Anwendung funktioniert:</span><span class="sxs-lookup"><span data-stu-id="651c6-308">For ASP.NET Core API to work with this convention in your application startup:</span></span>

* <span data-ttu-id="651c6-309">Konfigurieren Sie Ihre APP, um ein Token in einem :::no-loc(cookie)::: aufgerufenen bereitzustellen `XSRF-TOKEN` .</span><span class="sxs-lookup"><span data-stu-id="651c6-309">Configure your app to provide a token in a :::no-loc(cookie)::: called `XSRF-TOKEN`.</span></span>
* <span data-ttu-id="651c6-310">Konfigurieren Sie den antifälschungs Dienst für die Suche nach einem Header mit dem Namen `X-XSRF-TOKEN` .</span><span class="sxs-lookup"><span data-stu-id="651c6-310">Configure the antiforgery service to look for a header named `X-XSRF-TOKEN`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable :::no-loc(cookie):::, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.:::no-loc(Cookie):::s.Append("XSRF-TOKEN", tokens.RequestToken, 
                new :::no-loc(Cookie):::Options() { HttpOnly = false });
        }

        return next(context);
    });
}

public void ConfigureServices(IServiceCollection services)
{
    // Angular's default header name for sending the XSRF token.
    services.AddAntiforgery(options => options.HeaderName = "X-XSRF-TOKEN");
}
```

<span data-ttu-id="651c6-311">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="651c6-311">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="extend-antiforgery"></a><span data-ttu-id="651c6-312">Antifälschung erweitern</span><span class="sxs-lookup"><span data-stu-id="651c6-312">Extend antiforgery</span></span>

<span data-ttu-id="651c6-313">Der [iantiforgeryadditionaldataprovider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) -Typ ermöglicht es Entwicklern, das Verhalten des Anti-CSRF-Systems zu erweitern, indem zusätzliche Daten in jedem Token abgerundet werden.</span><span class="sxs-lookup"><span data-stu-id="651c6-313">The [IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) type allows developers to extend the behavior of the anti-CSRF system by round-tripping additional data in each token.</span></span> <span data-ttu-id="651c6-314">Die [getadditionaldata](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) -Methode wird jedes Mal aufgerufen, wenn ein Feld Token generiert wird, und der Rückgabewert wird in das generierte Token eingebettet.</span><span class="sxs-lookup"><span data-stu-id="651c6-314">The [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) method is called each time a field token is generated, and the return value is embedded within the generated token.</span></span> <span data-ttu-id="651c6-315">Ein Implementierer könnte einen Zeitstempel, eine Nonce oder einen anderen Wert zurückgeben und dann [validateadditionaldata](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) aufzurufen, um diese Daten zu validieren, wenn das Token überprüft wird.</span><span class="sxs-lookup"><span data-stu-id="651c6-315">An implementer could return a timestamp, a nonce, or any other value and then call [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) to validate this data when the token is validated.</span></span> <span data-ttu-id="651c6-316">Der Benutzername des Clients ist bereits in die generierten Token eingebettet, sodass es nicht erforderlich ist, diese Informationen einzubeziehen.</span><span class="sxs-lookup"><span data-stu-id="651c6-316">The client's username is already embedded in the generated tokens, so there's no need to include this information.</span></span> <span data-ttu-id="651c6-317">Wenn ein Token zusätzliche Daten enthält, aber keine `IAntiForgeryAdditionalDataProvider` konfiguriert ist, werden die ergänzenden Daten nicht überprüft.</span><span class="sxs-lookup"><span data-stu-id="651c6-317">If a token includes supplemental data but no `IAntiForgeryAdditionalDataProvider` is configured, the supplemental data isn't validated.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="651c6-318">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="651c6-318">Additional resources</span></span>

* <span data-ttu-id="651c6-319">[CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) für das [Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (OWASP).</span><span class="sxs-lookup"><span data-stu-id="651c6-319">[CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) on [Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (OWASP).</span></span>
* <xref:host-and-deploy/web-farm>
