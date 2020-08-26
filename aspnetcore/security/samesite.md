---
title: Arbeiten mit SameSite cookie s in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie mit SameSite cookie s in ASP.net Core
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
no-loc:
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
- Electron
uid: security/samesite
ms.openlocfilehash: 3ba033b4165b19131d11311e5ae9d64e6afe48ca
ms.sourcegitcommit: f09407d128634d200c893bfb1c163e87fa47a161
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88865431"
---
# <a name="work-with-samesite-no-loccookies-in-aspnet-core"></a><span data-ttu-id="f5543-103">Arbeiten mit SameSite cookie s in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="f5543-103">Work with SameSite cookies in ASP.NET Core</span></span>

<span data-ttu-id="f5543-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f5543-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f5543-105">SameSite ist ein [IETF](https://ietf.org/about/) -Entwurfs Standard, der Schutz vor Cross-Site Request fälschungstoken (CSRF) bietet.</span><span class="sxs-lookup"><span data-stu-id="f5543-105">SameSite is an [IETF](https://ietf.org/about/) draft standard designed to provide some protection against cross-site request forgery (CSRF) attacks.</span></span> <span data-ttu-id="f5543-106">Ursprünglich in [2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07)entworfen wurde, wurde der Entwurfs Standard in [2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00)aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="f5543-106">Originally drafted in [2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07), the draft standard was updated in [2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00).</span></span> <span data-ttu-id="f5543-107">Der aktualisierte Standard ist nicht abwärts kompatibel mit dem vorherigen Standard, und es gibt folgende Unterschiede:</span><span class="sxs-lookup"><span data-stu-id="f5543-107">The updated standard is not backward compatible with the previous standard, with the following being the most noticeable differences:</span></span>

* <span data-ttu-id="f5543-108">Cookies ohne SameSite-Header werden `SameSite=Lax` standardmäßig als behandelt.</span><span class="sxs-lookup"><span data-stu-id="f5543-108">Cookies without SameSite header are treated as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="f5543-109">`SameSite=None` muss verwendet werden, um eine standortübergreifende cookie Verwendung zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="f5543-109">`SameSite=None` must be used to allow cross-site cookie use.</span></span>
* <span data-ttu-id="f5543-110">CookieGibt an, dass Assert `SameSite=None` auch als markiert werden muss `Secure` .</span><span class="sxs-lookup"><span data-stu-id="f5543-110">Cookies that assert `SameSite=None` must also be marked as `Secure`.</span></span>
* <span data-ttu-id="f5543-111">Bei Anwendungen, die verwenden, treten [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) möglicherweise Probleme mit `sameSite=Lax` oder en auf, `sameSite=Strict` cookie da `<iframe>` als standortübergreifende Szenarios behandelt wird.</span><span class="sxs-lookup"><span data-stu-id="f5543-111">Applications that use [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) may experience issues with `sameSite=Lax` or `sameSite=Strict` cookies because `<iframe>` is treated as cross-site scenarios.</span></span>
* <span data-ttu-id="f5543-112">Der `SameSite=None` -Wert ist durch den [2016-Standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07) nicht zulässig und bewirkt, dass einige Implementierungen wie z. b cookie . behandeln `SameSite=Strict` .</span><span class="sxs-lookup"><span data-stu-id="f5543-112">The value `SameSite=None` is not allowed by the [2016 standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07) and causes some implementations to treat such cookies as `SameSite=Strict`.</span></span> <span data-ttu-id="f5543-113">Siehe [Unterstützung älterer Browser](#sob) in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="f5543-113">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="f5543-114">Die `SameSite=Lax` Einstellung funktioniert für die meisten Anwendungen cookie .</span><span class="sxs-lookup"><span data-stu-id="f5543-114">The `SameSite=Lax` setting works for most application cookies.</span></span> <span data-ttu-id="f5543-115">Für einige Formen der Authentifizierung wie [OpenID Connect](https://openid.net/connect/) (oidc) und [WS-](https://auth0.com/docs/protocols/ws-fed) Verbund werden standardmäßig Post basierte Umleitungen bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="f5543-115">Some forms of authentication like [OpenID Connect](https://openid.net/connect/) (OIDC) and [WS-Federation](https://auth0.com/docs/protocols/ws-fed) default to POST based redirects.</span></span> <span data-ttu-id="f5543-116">Die Post basierten Umleitungen veranlassen den SameSite-Browserschutz, sodass SameSite für diese Komponenten deaktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="f5543-116">The POST based redirects trigger the SameSite browser protections, so SameSite is disabled for these components.</span></span> <span data-ttu-id="f5543-117">Die meisten [OAuth](https://oauth.net/) -Anmeldungen sind aufgrund von Unterschieden in der Art der Anforderungs Abläufe nicht betroffen.</span><span class="sxs-lookup"><span data-stu-id="f5543-117">Most [OAuth](https://oauth.net/) logins are not affected due to differences in how the request flows.</span></span>

<span data-ttu-id="f5543-118">Jede ASP.net Core Komponente, die cookie s ausgibt, muss entscheiden, ob SameSite geeignet ist.</span><span class="sxs-lookup"><span data-stu-id="f5543-118">Each ASP.NET Core component that emits cookies needs to decide if SameSite is appropriate.</span></span>

## <a name="samesite-and-no-locidentity"></a><span data-ttu-id="f5543-119">SameSite und Identity</span><span class="sxs-lookup"><span data-stu-id="f5543-119">SameSite and Identity</span></span>

[!INCLUDE[](~/includes/SameSiteIdentity.md)]

## <a name="samesite-test-sample-code"></a><span data-ttu-id="f5543-120">SameSite-Testbeispiel Code</span><span class="sxs-lookup"><span data-stu-id="f5543-120">SameSite test sample code</span></span>

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="f5543-121">Die folgenden Beispiele können heruntergeladen und getestet werden:</span><span class="sxs-lookup"><span data-stu-id="f5543-121">The following samples can be downloaded and tested:</span></span>

| <span data-ttu-id="f5543-122">Beispiel</span><span class="sxs-lookup"><span data-stu-id="f5543-122">Sample</span></span>               | <span data-ttu-id="f5543-123">Dokument</span><span class="sxs-lookup"><span data-stu-id="f5543-123">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="f5543-124">.Net Core MVC</span><span class="sxs-lookup"><span data-stu-id="f5543-124">.NET Core MVC</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| <span data-ttu-id="f5543-125">[.Net Core- Razor Seiten](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span><span class="sxs-lookup"><span data-stu-id="f5543-125">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span></span>  | <xref:security/samesite/rp21> |

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f5543-126">Das folgende Beispiel kann heruntergeladen und getestet werden:</span><span class="sxs-lookup"><span data-stu-id="f5543-126">The following sample can be downloaded and tested:</span></span>


| <span data-ttu-id="f5543-127">Beispiel</span><span class="sxs-lookup"><span data-stu-id="f5543-127">Sample</span></span>               | <span data-ttu-id="f5543-128">Dokument</span><span class="sxs-lookup"><span data-stu-id="f5543-128">Document</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="f5543-129">[.Net Core- Razor Seiten](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span><span class="sxs-lookup"><span data-stu-id="f5543-129">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span></span>  | <xref:security/samesite/rp31> |

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="net-core-support-for-the-samesite-attribute"></a><span data-ttu-id="f5543-130">.Net Core-Unterstützung für das SameSite-Attribut</span><span class="sxs-lookup"><span data-stu-id="f5543-130">.NET Core support for the sameSite attribute</span></span>

<span data-ttu-id="f5543-131">.Net Core 2,2 und höhere Versionen unterstützen 2019 den Entwurf Standard für SameSite seit der Veröffentlichung von Updates im Dezember 2019.</span><span class="sxs-lookup"><span data-stu-id="f5543-131">.NET Core 2.2 and later support the 2019 draft standard for SameSite since the release of updates in December 2019.</span></span> <span data-ttu-id="f5543-132">Entwickler können den Wert des SameSite-Attributs Programm gesteuert mithilfe der- `HttpCookie.SameSite` Eigenschaft steuern.</span><span class="sxs-lookup"><span data-stu-id="f5543-132">Developers are able to programmatically control the value of the sameSite attribute using the `HttpCookie.SameSite` property.</span></span> <span data-ttu-id="f5543-133">Das Festlegen der- `SameSite` Eigenschaft auf Strict, Lax oder None führt dazu, dass die Werte mit dem im Netzwerk geschrieben werden cookie .</span><span class="sxs-lookup"><span data-stu-id="f5543-133">Setting the `SameSite` property to Strict, Lax, or None results in those values being written on the network with the cookie.</span></span> <span data-ttu-id="f5543-134">Wenn Sie den Wert auf festlegen, `(SameSiteMode)(-1)` wird angegeben, dass kein SameSite-Attribut im Netzwerk mit dem cookie</span><span class="sxs-lookup"><span data-stu-id="f5543-134">Setting it equal to `(SameSiteMode)(-1)` indicates that no sameSite attribute should be included on the network with the cookie</span></span>

[!code-csharp[](samesite/snippets/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="f5543-135">.Net Core 3,0 und höhere Versionen unterstützen die aktualisierten SameSite-Werte und fügen der Enumeration einen zusätzlichen Enumerationswert hinzu `SameSiteMode.Unspecified` `SameSiteMode` .</span><span class="sxs-lookup"><span data-stu-id="f5543-135">.NET Core 3.0 and later support the updated SameSite values and adds an extra enum value, `SameSiteMode.Unspecified` to the `SameSiteMode` enum.</span></span>
<span data-ttu-id="f5543-136">Dieser neue Wert gibt an, dass keine SameSite mit dem gesendet werden soll cookie .</span><span class="sxs-lookup"><span data-stu-id="f5543-136">This new value indicates no sameSite should be sent with the cookie.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

## <a name="december-patch-behavior-changes"></a><span data-ttu-id="f5543-137">Änderungen im Dezember-patchverhalten</span><span class="sxs-lookup"><span data-stu-id="f5543-137">December patch behavior changes</span></span>

<span data-ttu-id="f5543-138">Die spezifische Behavior Change für .NET Framework und .net Core 2,1 gibt an, wie die- `SameSite` Eigenschaft den `None` Wert interpretiert.</span><span class="sxs-lookup"><span data-stu-id="f5543-138">The specific behavior change for .NET Framework and .NET Core 2.1 is how the `SameSite` property interprets the `None` value.</span></span> <span data-ttu-id="f5543-139">Vor dem Patch bedeutete der Wert " `None` Geben Sie das Attribut überhaupt nicht aus". nach dem Patch bedeutet dies, dass das Attribut mit dem Wert "" ausgegeben wird `None` .</span><span class="sxs-lookup"><span data-stu-id="f5543-139">Before the patch a value of `None` meant "Do not emit the attribute at all", after the patch it means "Emit the attribute with a value of `None`".</span></span> <span data-ttu-id="f5543-140">Nach dem Patch `SameSite` bewirkt der Wert `(SameSiteMode)(-1)` , dass das Attribut nicht ausgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="f5543-140">After the patch a `SameSite` value of `(SameSiteMode)(-1)` causes the attribute not to be emitted.</span></span>

<span data-ttu-id="f5543-141">Der Standardwert für SameSite für die Formular Authentifizierung und den Sitzungszustand cookie s wurde von `None` in geändert `Lax` .</span><span class="sxs-lookup"><span data-stu-id="f5543-141">The default SameSite value for forms authentication and session state cookies was changed from `None` to `Lax`.</span></span>

::: moniker-end

## <a name="api-usage-with-samesite"></a><span data-ttu-id="f5543-142">API-Nutzung mit SameSite</span><span class="sxs-lookup"><span data-stu-id="f5543-142">API usage with SameSite</span></span>

<span data-ttu-id="f5543-143">[HttpContext. Response. Cookie s. Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) ist standardmäßig, d. h. `Unspecified` , es wird kein SameSite-Attribut hinzugefügt, cookie und der Client verwendet sein Standardverhalten (LAX für neue Browser, keine für die alten).</span><span class="sxs-lookup"><span data-stu-id="f5543-143">[HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) defaults to `Unspecified`, meaning no SameSite attribute added to the cookie and the client will use its default behavior (Lax for new browsers, None for old ones).</span></span> <span data-ttu-id="f5543-144">Der folgende Code zeigt, wie der cookie Wert von SameSite in geändert wird `SameSiteMode.Lax` :</span><span class="sxs-lookup"><span data-stu-id="f5543-144">The following code shows how to change the cookie SameSite value to `SameSiteMode.Lax`:</span></span>

[!code-csharp[](samesite/sample/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="f5543-145">Alle ASP.net Core Komponenten, die cookie s ausgeben, überschreiben die vorangehenden Standardwerte mit Einstellungen, die für Ihre Szenarien geeignet sind</span><span class="sxs-lookup"><span data-stu-id="f5543-145">All ASP.NET Core components that emit cookies override the preceding defaults with settings appropriate for their scenarios.</span></span> <span data-ttu-id="f5543-146">Die überschriebenen vorangehenden Standardwerte haben sich nicht geändert.</span><span class="sxs-lookup"><span data-stu-id="f5543-146">The overridden preceding default values haven't changed.</span></span>

| <span data-ttu-id="f5543-147">Komponente</span><span class="sxs-lookup"><span data-stu-id="f5543-147">Component</span></span> | cookie | <span data-ttu-id="f5543-148">Standard</span><span class="sxs-lookup"><span data-stu-id="f5543-148">Default</span></span> |
| ------------- | ------------- |
| <xref:Microsoft.AspNetCore.Http.CookieBuilder> | <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> | `Unspecified` |
| <xref:Microsoft.AspNetCore.Http.HttpContext.Session>  | <span data-ttu-id="f5543-149">[SessionOptions.Cookie](xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie)</span><span class="sxs-lookup"><span data-stu-id="f5543-149">[SessionOptions.Cookie](xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie)</span></span> |`Lax` |
| <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider>  | <span data-ttu-id="f5543-150">[CookieTempdataprovideroptions.Cookie](xref:Microsoft.AspNetCore.Mvc.CookieTempDataProviderOptions.Cookie)</span><span class="sxs-lookup"><span data-stu-id="f5543-150">[CookieTempDataProviderOptions.Cookie](xref:Microsoft.AspNetCore.Mvc.CookieTempDataProviderOptions.Cookie)</span></span> | `Lax` |
| <xref:Microsoft.AspNetCore.Antiforgery.IAntiforgery> | <span data-ttu-id="f5543-151">[Antiforgeryoptions.Cookie](xref:Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions.Cookie)</span><span class="sxs-lookup"><span data-stu-id="f5543-151">[AntiforgeryOptions.Cookie](xref:Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions.Cookie)</span></span>| `Strict` |
| <span data-ttu-id="f5543-152">[Cookie Genehmigung](xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*)</span><span class="sxs-lookup"><span data-stu-id="f5543-152">[Cookie Authentication](xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*)</span></span> | <span data-ttu-id="f5543-153">[CookieAuthenticationoptions.Cookie](xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.CookieName)</span><span class="sxs-lookup"><span data-stu-id="f5543-153">[CookieAuthenticationOptions.Cookie](xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.CookieName)</span></span> | `Lax` |
| <xref:Microsoft.Extensions.DependencyInjection.TwitterExtensions.AddTwitter*> | <span data-ttu-id="f5543-154">[Twitteroptions. State Cookie](xref:Microsoft.AspNetCore.Authentication.Twitter.TwitterOptions.StateCookie)</span><span class="sxs-lookup"><span data-stu-id="f5543-154">[TwitterOptions.StateCookie ](xref:Microsoft.AspNetCore.Authentication.Twitter.TwitterOptions.StateCookie)</span></span> | `Lax`  |
| <span data-ttu-id="f5543-155"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [Remoteauthenticationoptions. KorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span><span class="sxs-lookup"><span data-stu-id="f5543-155"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions.CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.OpenIdConnectExtensions.AddOpenIdConnect*> | <span data-ttu-id="f5543-156">[Openidconnectoptions. NonceCookie](xref:Microsoft.AspNetCore.Authentication.OpenIdConnect.OpenIdConnectOptions.NonceCookie)</span><span class="sxs-lookup"><span data-stu-id="f5543-156">[OpenIdConnectOptions.NonceCookie](xref:Microsoft.AspNetCore.Authentication.OpenIdConnect.OpenIdConnectOptions.NonceCookie)</span></span>| `None` |
| <span data-ttu-id="f5543-157">[HttpContext. Response. Cookie s. Anfügen](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span><span class="sxs-lookup"><span data-stu-id="f5543-157">[HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span></span> | <xref:Microsoft.AspNetCore.Http.CookieOptions> | `Unspecified` |

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="f5543-158">ASP.net Core 3,1 und höher bietet die folgende SameSite-Unterstützung:</span><span class="sxs-lookup"><span data-stu-id="f5543-158">ASP.NET Core 3.1 and later provides the following SameSite support:</span></span>

* <span data-ttu-id="f5543-159">Definiert das auszugebende Verhalten `SameSiteMode.None` von neu. `SameSite=None`</span><span class="sxs-lookup"><span data-stu-id="f5543-159">Redefines the behavior of `SameSiteMode.None` to emit `SameSite=None`</span></span>
* <span data-ttu-id="f5543-160">Fügt einen neuen Wert `SameSiteMode.Unspecified` zum Weglassen des SameSite-Attributs hinzu.</span><span class="sxs-lookup"><span data-stu-id="f5543-160">Adds a new value `SameSiteMode.Unspecified` to omit the SameSite attribute.</span></span>
* <span data-ttu-id="f5543-161">Alle cookie s-APIs werden standardmäßig auf eingestellt `Unspecified` .</span><span class="sxs-lookup"><span data-stu-id="f5543-161">All cookies APIs default to `Unspecified`.</span></span> <span data-ttu-id="f5543-162">Einige Komponenten, die cookie s verwenden, legen die Werte spezifischer für Ihre Szenarios fest.</span><span class="sxs-lookup"><span data-stu-id="f5543-162">Some components that use cookies set values more specific to their scenarios.</span></span> <span data-ttu-id="f5543-163">Beispiele finden Sie in der obigen Tabelle.</span><span class="sxs-lookup"><span data-stu-id="f5543-163">See the table above for examples.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f5543-164">In ASP.net Core 3,0 und höher wurden die SameSite-Standardwerte geändert, um Konflikte mit inkonsistenten Client Standardwerten zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="f5543-164">In ASP.NET Core 3.0 and later the SameSite defaults were changed to avoid conflicting with inconsistent client defaults.</span></span> <span data-ttu-id="f5543-165">Die folgenden APIs haben den Standardwert von `SameSiteMode.Lax ` in geändert `-1` , um zu vermeiden, dass ein SameSite-Attribut für diese s ausgegeben wird cookie :</span><span class="sxs-lookup"><span data-stu-id="f5543-165">The following APIs have changed the default from `SameSiteMode.Lax ` to `-1` to avoid emitting a SameSite attribute for these cookies:</span></span>

* <span data-ttu-id="f5543-166"><xref:Microsoft.AspNetCore.Http.CookieOptions> wird mit " [HttpContext. Response" verwendet. Cookie s. Anfügen](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span><span class="sxs-lookup"><span data-stu-id="f5543-166"><xref:Microsoft.AspNetCore.Http.CookieOptions> used with [HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span></span>
* <span data-ttu-id="f5543-167"><xref:Microsoft.AspNetCore.Http.CookieBuilder>  wird als Factory für `CookieOptions`</span><span class="sxs-lookup"><span data-stu-id="f5543-167"><xref:Microsoft.AspNetCore.Http.CookieBuilder>  used as a factory for `CookieOptions`</span></span>
* <span data-ttu-id="f5543-168">[CookiePolicyoptions. minimumsamesitepolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)</span><span class="sxs-lookup"><span data-stu-id="f5543-168">[CookiePolicyOptions.MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)</span></span>

::: moniker-end

## <a name="history-and-changes"></a><span data-ttu-id="f5543-169">Verlauf und Änderungen</span><span class="sxs-lookup"><span data-stu-id="f5543-169">History and changes</span></span>

<span data-ttu-id="f5543-170">Die SameSite-Unterstützung wurde zunächst in ASP.net Core in 2,0 mithilfe des [Entwurfs Standards 2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1)implementiert.</span><span class="sxs-lookup"><span data-stu-id="f5543-170">SameSite support was first implemented in ASP.NET Core in 2.0 using the [2016 draft standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1).</span></span> <span data-ttu-id="f5543-171">Der 2016-Standard war Opt-in.</span><span class="sxs-lookup"><span data-stu-id="f5543-171">The 2016 standard was opt-in.</span></span> <span data-ttu-id="f5543-172">ASP.net Core durch Festlegen mehrerer cookie s auf `Lax` standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="f5543-172">ASP.NET Core opted-in by setting several cookies to `Lax` by default.</span></span> <span data-ttu-id="f5543-173">Nachdem mehrere Authentifizierungs [Probleme](https://github.com/aspnet/Announcements/issues/318) aufgetreten sind, wurde die meisten SameSite-Nutzung [deaktiviert](https://github.com/aspnet/Announcements/issues/348).</span><span class="sxs-lookup"><span data-stu-id="f5543-173">After encountering several [issues](https://github.com/aspnet/Announcements/issues/318) with authentication, most SameSite usage was [disabled](https://github.com/aspnet/Announcements/issues/348).</span></span>

<span data-ttu-id="f5543-174">Im November 2019 wurden [Patches](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) zum Aktualisieren von der 2016-Standard-auf den 2019-Standard ausgegeben.</span><span class="sxs-lookup"><span data-stu-id="f5543-174">[Patches](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) were issued in November 2019 to update from the 2016 standard to the 2019 standard.</span></span> <span data-ttu-id="f5543-175">Der [2019-Entwurf der SameSite-Spezifikation](https://github.com/aspnet/Announcements/issues/390):</span><span class="sxs-lookup"><span data-stu-id="f5543-175">The [2019 draft of the SameSite specification](https://github.com/aspnet/Announcements/issues/390):</span></span>

* <span data-ttu-id="f5543-176">Ist **nicht** abwärts kompatibel mit dem 2016-Entwurf.</span><span class="sxs-lookup"><span data-stu-id="f5543-176">Is **not** backwards compatible with the 2016 draft.</span></span> <span data-ttu-id="f5543-177">Weitere Informationen finden Sie [unter unterstützen älterer Browser](#sob) in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="f5543-177">For more information, see [Supporting older browsers](#sob) in this document.</span></span>
* <span data-ttu-id="f5543-178">Gibt cookie an, dass s `SameSite=Lax` Standardmäßig behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="f5543-178">Specifies cookies are treated as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="f5543-179">Gibt cookie die an, die explizit bestätigt werden, damit `SameSite=None` die standortübergreifende Übermittlung als markiert werden kann `Secure` .</span><span class="sxs-lookup"><span data-stu-id="f5543-179">Specifies cookies that explicitly assert `SameSite=None` in order to enable cross-site delivery should be marked as `Secure`.</span></span> <span data-ttu-id="f5543-180">`None` ist ein neuer Eintrag zum ablehnen.</span><span class="sxs-lookup"><span data-stu-id="f5543-180">`None` is a new entry to opt out.</span></span>
* <span data-ttu-id="f5543-181">Wird von Patches unterstützt, die für ASP.net Core 2,1, 2,2 und 3,0 ausgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="f5543-181">Is supported by patches issued for ASP.NET Core 2.1, 2.2, and 3.0.</span></span> <span data-ttu-id="f5543-182">ASP.net Core 3,1 bietet zusätzliche Unterstützung für SameSite.</span><span class="sxs-lookup"><span data-stu-id="f5543-182">ASP.NET Core 3.1 has additional SameSite support.</span></span>
* <span data-ttu-id="f5543-183">Ist für die standardmäßige Aktivierung durch [Chrome](https://chromestatus.com/feature/5088147346030592) in [Feb 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)vorgesehen.</span><span class="sxs-lookup"><span data-stu-id="f5543-183">Is scheduled to be enabled by [Chrome](https://chromestatus.com/feature/5088147346030592) by default in [Feb 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html).</span></span> <span data-ttu-id="f5543-184">Die Umstellung auf diesen Standard in 2019 wurde gestartet.</span><span class="sxs-lookup"><span data-stu-id="f5543-184">Browsers started moving to this standard in 2019.</span></span>

## <a name="apis-impacted-by-the-change-from-the-2016-samesite-draft-standard-to-the-2019-draft-standard"></a><span data-ttu-id="f5543-185">Von der Änderung von 2016 SameSite Draft Standard zum 2019 Draft Standard betroffene APIs</span><span class="sxs-lookup"><span data-stu-id="f5543-185">APIs impacted by the change from the 2016 SameSite draft standard to the 2019 draft standard</span></span>

* [<span data-ttu-id="f5543-186">Http. samesitemode</span><span class="sxs-lookup"><span data-stu-id="f5543-186">Http.SameSiteMode</span></span>](xref:Microsoft.AspNetCore.Http.SameSiteMode)
* <span data-ttu-id="f5543-187">[CookieOptionen. SameSite](xref:Microsoft.AspNetCore.Http.CookieOptions.SameSite)</span><span class="sxs-lookup"><span data-stu-id="f5543-187">[CookieOptions.SameSite](xref:Microsoft.AspNetCore.Http.CookieOptions.SameSite)</span></span>
* <span data-ttu-id="f5543-188">[CookieBuilder. SameSite](xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite)</span><span class="sxs-lookup"><span data-stu-id="f5543-188">[CookieBuilder.SameSite](xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite)</span></span>
* <span data-ttu-id="f5543-189">[CookiePolicyoptions. minimumsamesitepolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)</span><span class="sxs-lookup"><span data-stu-id="f5543-189">[CookiePolicyOptions.MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)</span></span>
* <xref:Microsoft.Net.Http.Headers.SameSiteMode?displayProperty=fullName>
* <xref:Microsoft.Net.Http.Headers.SetCookieHeaderValue.SameSite?displayProperty=fullName>

<a name="sob"></a>

## <a name="supporting-older-browsers"></a><span data-ttu-id="f5543-190">Unterstützung älterer Browser</span><span class="sxs-lookup"><span data-stu-id="f5543-190">Supporting older browsers</span></span>

<span data-ttu-id="f5543-191">Der 2016 SameSite-Standard hat festgestellt, dass unbekannte Werte als Werte behandelt werden müssen `SameSite=Strict` .</span><span class="sxs-lookup"><span data-stu-id="f5543-191">The 2016 SameSite standard mandated that unknown values must be treated as `SameSite=Strict` values.</span></span> <span data-ttu-id="f5543-192">Apps, auf die von älteren Browsern zugegriffen wird, die den 2016 SameSite-Standard unterstützen, können unterbrechen, wenn Sie eine SameSite-Eigenschaft mit dem Wert erhalten `None` .</span><span class="sxs-lookup"><span data-stu-id="f5543-192">Apps accessed from older browsers which support the 2016 SameSite standard may break when they get a SameSite property with a value of `None`.</span></span> <span data-ttu-id="f5543-193">Web-Apps müssen die Browser Erkennung implementieren, wenn Sie ältere Browser unterstützen möchten.</span><span class="sxs-lookup"><span data-stu-id="f5543-193">Web apps must implement browser detection if they intend to support older browsers.</span></span> <span data-ttu-id="f5543-194">ASP.net Core implementiert die Browser Erkennung nicht, da die Werte von Benutzer-Agents stark flüchtig sind und häufig geändert werden.</span><span class="sxs-lookup"><span data-stu-id="f5543-194">ASP.NET Core doesn't implement browser detection because User-Agents values are highly volatile and change frequently.</span></span> <span data-ttu-id="f5543-195">Ein Erweiterungs Punkt in <xref:Microsoft.AspNetCore.CookiePolicy> ermöglicht das Plug in der Benutzer-Agent-spezifischen Logik.</span><span class="sxs-lookup"><span data-stu-id="f5543-195">An extension point in <xref:Microsoft.AspNetCore.CookiePolicy> allows plugging in User-Agent specific logic.</span></span>

<span data-ttu-id="f5543-196">`Startup.Configure`Fügen Sie in Code hinzu, der aufruft, <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> bevor aufgerufen wird, <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> oder *eine* Methode, die cookie s schreibt:</span><span class="sxs-lookup"><span data-stu-id="f5543-196">In `Startup.Configure`, add code that calls <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> before calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> or *any* method that writes cookies:</span></span>

[!code-csharp[](samesite/sample/Startup.cs?name=snippet5&highlight=18-19)]

<span data-ttu-id="f5543-197">`Startup.ConfigureServices`Fügen Sie in Code ähnlich dem folgenden hinzu:</span><span class="sxs-lookup"><span data-stu-id="f5543-197">In `Startup.ConfigureServices`, add code similar to the following:</span></span>

::: moniker range="= aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup.cs?name=snippet)]

::: moniker-end

<span data-ttu-id="f5543-198">Im vorherigen Beispiel `MyUserAgentDetectionLib.DisallowsSameSiteNone` ist eine vom Benutzer bereitgestellte Bibliothek, die erkennt, ob der Benutzer-Agent SameSite nicht unterstützt `None` :</span><span class="sxs-lookup"><span data-stu-id="f5543-198">In the preceding sample, `MyUserAgentDetectionLib.DisallowsSameSiteNone` is a user supplied library that detects if the user agent doesn't support SameSite `None`:</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet2)]

<span data-ttu-id="f5543-199">Der folgende Code zeigt eine Beispiel `DisallowsSameSiteNone` Methode:</span><span class="sxs-lookup"><span data-stu-id="f5543-199">The following code shows a sample `DisallowsSameSiteNone` method:</span></span>

> [!WARNING]
> <span data-ttu-id="f5543-200">Der folgende Code dient nur zu Demonstrationszwecken:</span><span class="sxs-lookup"><span data-stu-id="f5543-200">The following code is for demonstration only:</span></span>
> * <span data-ttu-id="f5543-201">Er sollte nicht als "Fertig" betrachtet werden.</span><span class="sxs-lookup"><span data-stu-id="f5543-201">It should not be considered complete.</span></span>
> * <span data-ttu-id="f5543-202">Er wird nicht verwaltet oder unterstützt.</span><span class="sxs-lookup"><span data-stu-id="f5543-202">It is not maintained or supported.</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippetX)]

## <a name="test-apps-for-samesite-problems"></a><span data-ttu-id="f5543-203">Testen von Apps für SameSite-Probleme</span><span class="sxs-lookup"><span data-stu-id="f5543-203">Test apps for SameSite problems</span></span>

<span data-ttu-id="f5543-204">Apps, die mit Remote Standorten interagieren, z. b. durch die Anmeldung von Drittanbietern, benötigen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="f5543-204">Apps that interact with remote sites such as through third-party login need to:</span></span>

* <span data-ttu-id="f5543-205">Testen Sie die Interaktion in mehreren Browsern.</span><span class="sxs-lookup"><span data-stu-id="f5543-205">Test the interaction on multiple browsers.</span></span>
* <span data-ttu-id="f5543-206">Wenden Sie die in diesem Dokument erörterte [ Cookie Erkennung und Entschärfung des Richtlinien Browsers](#sob) an.</span><span class="sxs-lookup"><span data-stu-id="f5543-206">Apply the [CookiePolicy browser detection and mitigation](#sob) discussed in this document.</span></span>

<span data-ttu-id="f5543-207">Testen Sie Web-Apps mithilfe einer Client Version, die das neue SameSite-Verhalten abonnieren kann.</span><span class="sxs-lookup"><span data-stu-id="f5543-207">Test web apps using a client version that can opt-in to the new SameSite behavior.</span></span> <span data-ttu-id="f5543-208">Chrome, Firefox und Chromium Edge verfügen über neue Feature-Flags, die für Tests verwendet werden können.</span><span class="sxs-lookup"><span data-stu-id="f5543-208">Chrome, Firefox, and Chromium Edge all have new opt-in feature flags that can be used for testing.</span></span> <span data-ttu-id="f5543-209">Nachdem Ihre APP die SameSite-Patches angewendet hat, testen Sie Sie mit älteren Client Versionen, insbesondere Safari.</span><span class="sxs-lookup"><span data-stu-id="f5543-209">After your app applies the SameSite patches, test it with older client versions, especially Safari.</span></span> <span data-ttu-id="f5543-210">Weitere Informationen finden Sie [unter unterstützen älterer Browser](#sob) in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="f5543-210">For more information, see [Supporting older browsers](#sob) in this document.</span></span>

### <a name="test-with-chrome"></a><span data-ttu-id="f5543-211">Testen mit Chrome</span><span class="sxs-lookup"><span data-stu-id="f5543-211">Test with Chrome</span></span>

<span data-ttu-id="f5543-212">Chrome 78 + liefert irreführende Ergebnisse, da eine vorübergehende Entschärfung vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="f5543-212">Chrome 78+ gives misleading results because it has a temporary mitigation in place.</span></span> <span data-ttu-id="f5543-213">Die temporäre Chrome-Minderung von 78 und höher ermöglicht es, dass cookie weniger als zwei Minuten alt sind.</span><span class="sxs-lookup"><span data-stu-id="f5543-213">The Chrome 78+ temporary mitigation allows cookies less than two minutes old.</span></span> <span data-ttu-id="f5543-214">Chrome 76 oder 77 mit aktivierten geeigneten testflags bietet genauere Ergebnisse.</span><span class="sxs-lookup"><span data-stu-id="f5543-214">Chrome 76 or 77 with the appropriate test flags enabled provides more accurate results.</span></span> <span data-ttu-id="f5543-215">Zum Testen des neuen SameSite-Verhaltens umschalten `chrome://flags/#same-site-by-default-cookies` in **aktiviert**.</span><span class="sxs-lookup"><span data-stu-id="f5543-215">To test the new SameSite behavior toggle `chrome://flags/#same-site-by-default-cookies` to **Enabled**.</span></span> <span data-ttu-id="f5543-216">Ältere Versionen von Chrome (75 und niedriger) werden mit der neuen `None` Einstellung gemeldet.</span><span class="sxs-lookup"><span data-stu-id="f5543-216">Older versions of Chrome (75 and below) are reported to fail with the new `None` setting.</span></span> <span data-ttu-id="f5543-217">Siehe [Unterstützung älterer Browser](#sob) in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="f5543-217">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="f5543-218">Google stellt keine älteren Chrome-Versionen zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="f5543-218">Google does not make older chrome versions available.</span></span> <span data-ttu-id="f5543-219">Befolgen Sie die Anweisungen unter [Herunterladen von Chrom](https://www.chromium.org/getting-involved/download-chromium) , um ältere Versionen von Chrome zu testen.</span><span class="sxs-lookup"><span data-stu-id="f5543-219">Follow the instructions at [Download Chromium](https://www.chromium.org/getting-involved/download-chromium) to test older versions of Chrome.</span></span> <span data-ttu-id="f5543-220">Laden Sie Chrome **nicht** von den von Ihnen bereitgestellten Links herunter, indem Sie nach älteren Versionen von Chrome suchen.</span><span class="sxs-lookup"><span data-stu-id="f5543-220">Do **not** download Chrome from links provided by searching for older versions of chrome.</span></span>

* [<span data-ttu-id="f5543-221">Chromium 76 Win64</span><span class="sxs-lookup"><span data-stu-id="f5543-221">Chromium 76 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/664998/)
* [<span data-ttu-id="f5543-222">Chromium 74 Win64</span><span class="sxs-lookup"><span data-stu-id="f5543-222">Chromium 74 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/638880/)

<span data-ttu-id="f5543-223">Beginnend mit der Canary-Version `80.0.3975.0` kann die vorübergehende Entschärfung von Lax und Post zu Testzwecken deaktiviert werden. dazu wird das neue Flag verwendet `--enable-features=SameSiteDefaultChecksMethodRigorously` , um das Testen von Websites und Diensten im Endzustand des Features zuzulassen, bei dem die Entschärfung entfernt wurde.</span><span class="sxs-lookup"><span data-stu-id="f5543-223">Starting in Canary version `80.0.3975.0`, the Lax+POST temporary mitigation can be disabled for testing purposes using the new flag `--enable-features=SameSiteDefaultChecksMethodRigorously` to allow testing of sites and services in the eventual end state of the feature where the mitigation has been removed.</span></span> <span data-ttu-id="f5543-224">Weitere Informationen finden Sie in den Informationen zu den [Software Update](https://www.chromium.org/updates/same-site) -Projekten in der Projektwebsite.</span><span class="sxs-lookup"><span data-stu-id="f5543-224">For more information, see The Chromium Projects [SameSite Updates](https://www.chromium.org/updates/same-site)</span></span>

### <a name="test-with-safari"></a><span data-ttu-id="f5543-225">Testen mit Safari</span><span class="sxs-lookup"><span data-stu-id="f5543-225">Test with Safari</span></span>

<span data-ttu-id="f5543-226">Safari 12 hat den vorherigen Entwurf streng implementiert und schlägt fehl, wenn sich der neue `None` Wert in einem befindet cookie .</span><span class="sxs-lookup"><span data-stu-id="f5543-226">Safari 12 strictly implemented the prior draft and fails when the new `None` value is in a cookie.</span></span> <span data-ttu-id="f5543-227">`None` wird über den Browser Erkennungs Code, der ältere Browser in diesem Dokument [unterstützt](#sob) , vermieden.</span><span class="sxs-lookup"><span data-stu-id="f5543-227">`None` is avoided via the browser detection code [Supporting older browsers](#sob) in this document.</span></span> <span data-ttu-id="f5543-228">Testen Sie Safari 12-, Safari 13-und WebKit-basierte Anmeldungen im Betriebssystem Format unter Verwendung von msal, Adal oder einer beliebigen Bibliothek, die Sie verwenden.</span><span class="sxs-lookup"><span data-stu-id="f5543-228">Test Safari 12, Safari 13, and WebKit based OS style logins using MSAL, ADAL or whatever library you are using.</span></span> <span data-ttu-id="f5543-229">Das Problem hängt von der zugrunde liegenden Betriebssystemversion ab.</span><span class="sxs-lookup"><span data-stu-id="f5543-229">The problem is dependent on the underlying OS version.</span></span> <span data-ttu-id="f5543-230">OSX-mujave (10,14) und IOS 12 haben bekanntermaßen Kompatibilitätsprobleme mit dem neuen SameSite-Verhalten.</span><span class="sxs-lookup"><span data-stu-id="f5543-230">OSX Mojave (10.14) and iOS 12 are known to have compatibility problems with the new SameSite behavior.</span></span> <span data-ttu-id="f5543-231">Durch ein Upgrade des Betriebssystems auf OSX Catalina (10,15) oder IOS 13 wird das Problem behoben.</span><span class="sxs-lookup"><span data-stu-id="f5543-231">Upgrading the OS to OSX Catalina (10.15) or iOS 13 fixes the problem.</span></span> <span data-ttu-id="f5543-232">Safari verfügt derzeit nicht über ein Opt-in-Flag zum Testen des neuen Spezifikations Verhaltens.</span><span class="sxs-lookup"><span data-stu-id="f5543-232">Safari does not currently have an opt-in flag for testing the new spec behavior.</span></span>

### <a name="test-with-firefox"></a><span data-ttu-id="f5543-233">Testen mit Firefox</span><span class="sxs-lookup"><span data-stu-id="f5543-233">Test with Firefox</span></span>

<span data-ttu-id="f5543-234">Die Firefox-Unterstützung für den neuen Standard kann auf Version 68 + getestet werden, indem Sie sich auf der `about:config` Seite mit dem Feature-Flag anmelden `network.cookie.sameSite.laxByDefault` .</span><span class="sxs-lookup"><span data-stu-id="f5543-234">Firefox support for the new standard can be tested on version 68+ by opting in on the `about:config` page with the feature flag `network.cookie.sameSite.laxByDefault`.</span></span> <span data-ttu-id="f5543-235">Es gab keine Berichte über Kompatibilitätsprobleme mit älteren Versionen von Firefox.</span><span class="sxs-lookup"><span data-stu-id="f5543-235">There haven't been reports of compatibility issues with older versions of Firefox.</span></span>

### <a name="test-with-edge-browser"></a><span data-ttu-id="f5543-236">Testen mit dem Edge-Browser</span><span class="sxs-lookup"><span data-stu-id="f5543-236">Test with Edge browser</span></span>

<span data-ttu-id="f5543-237">Edge unterstützt den alten SameSite-Standard.</span><span class="sxs-lookup"><span data-stu-id="f5543-237">Edge supports the old SameSite standard.</span></span> <span data-ttu-id="f5543-238">Edge Version 44 hat keine bekannten Kompatibilitätsprobleme mit dem neuen Standard.</span><span class="sxs-lookup"><span data-stu-id="f5543-238">Edge version 44 doesn't have any known compatibility problems with the new standard.</span></span>

### <a name="test-with-edge-chromium"></a><span data-ttu-id="f5543-239">Testen mit Edge (Chrom)</span><span class="sxs-lookup"><span data-stu-id="f5543-239">Test with Edge (Chromium)</span></span>

<span data-ttu-id="f5543-240">SameSite-Flags werden auf der `edge://flags/#same-site-by-default-cookies` Seite festgelegt.</span><span class="sxs-lookup"><span data-stu-id="f5543-240">SameSite flags are set on the `edge://flags/#same-site-by-default-cookies` page.</span></span> <span data-ttu-id="f5543-241">Es wurden keine Kompatibilitätsprobleme mit Edge-Chrom erkannt.</span><span class="sxs-lookup"><span data-stu-id="f5543-241">No compatibility issues were discovered with Edge Chromium.</span></span>

### <a name="test-with-no-locelectron"></a><span data-ttu-id="f5543-242">Testen mit Electron</span><span class="sxs-lookup"><span data-stu-id="f5543-242">Test with Electron</span></span>

<span data-ttu-id="f5543-243">Zu den Versionen von Electron zählen ältere Versionen von Chrom.</span><span class="sxs-lookup"><span data-stu-id="f5543-243">Versions of Electron include older versions of Chromium.</span></span> <span data-ttu-id="f5543-244">Beispielsweise Electron ist die von Teams verwendete-Version Chrom 66, das das ältere Verhalten zeigt.</span><span class="sxs-lookup"><span data-stu-id="f5543-244">For example, the version of Electron used by Teams is Chromium 66, which exhibits the older behavior.</span></span> <span data-ttu-id="f5543-245">Sie müssen ihre eigenen Kompatibilitätstests mit der Produktversion durchführen Electron .</span><span class="sxs-lookup"><span data-stu-id="f5543-245">You must perform your own compatibility testing with the version of Electron your product uses.</span></span> <span data-ttu-id="f5543-246">Weitere Informationen finden Sie [unter Unterstützung älterer Browser](#sob) im folgenden Abschnitt.</span><span class="sxs-lookup"><span data-stu-id="f5543-246">See [Supporting older browsers](#sob) in the following section.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f5543-247">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="f5543-247">Additional resources</span></span>

* [<span data-ttu-id="f5543-248">Chromium-Blog: Entwickler: machen Sie sich bereit für neue SameSite = None; Sichere Cookie Einstellungen</span><span class="sxs-lookup"><span data-stu-id="f5543-248">Chromium Blog:Developers: Get Ready for New SameSite=None; Secure Cookie Settings</span></span>](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)
* <span data-ttu-id="f5543-249">["SameSite cookie s" erläutert](https://web.dev/samesite-cookies-explained/)</span><span class="sxs-lookup"><span data-stu-id="f5543-249">[SameSite cookies explained](https://web.dev/samesite-cookies-explained/)</span></span>
* [<span data-ttu-id="f5543-250">Patches vom November 2019</span><span class="sxs-lookup"><span data-stu-id="f5543-250">November 2019 Patches</span></span>](https://devblogs.microsoft.com/dotnet/net-core-November-2019/)

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

| <span data-ttu-id="f5543-251">Beispiel</span><span class="sxs-lookup"><span data-stu-id="f5543-251">Sample</span></span>               | <span data-ttu-id="f5543-252">Dokument</span><span class="sxs-lookup"><span data-stu-id="f5543-252">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="f5543-253">.Net Core MVC</span><span class="sxs-lookup"><span data-stu-id="f5543-253">.NET Core MVC</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| <span data-ttu-id="f5543-254">[.Net Core- Razor Seiten](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span><span class="sxs-lookup"><span data-stu-id="f5543-254">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span></span>  | <xref:security/samesite/rp21> |

::: moniker-end

 ::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="f5543-255">Beispiel</span><span class="sxs-lookup"><span data-stu-id="f5543-255">Sample</span></span>               | <span data-ttu-id="f5543-256">Dokument</span><span class="sxs-lookup"><span data-stu-id="f5543-256">Document</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="f5543-257">[.Net Core- Razor Seiten](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span><span class="sxs-lookup"><span data-stu-id="f5543-257">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span></span>  | <xref:security/samesite/rp31> |

::: moniker-end
