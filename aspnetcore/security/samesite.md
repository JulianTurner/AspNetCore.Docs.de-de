---
title: Arbeiten mit SameSite cookie s in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie mit SameSite cookie s in ASP.net Core
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
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
- Electron
uid: security/samesite
ms.openlocfilehash: f1aa388015bd540a6fda263eac53753ada63bf79
ms.sourcegitcommit: bce62ceaac7782e22d185814f2e8532c84efa472
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94673990"
---
# <a name="work-with-samesite-no-loccookies-in-aspnet-core"></a>Arbeiten mit SameSite cookie s in ASP.net Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

SameSite ist ein [IETF](https://ietf.org/about/) -Entwurfs Standard, der Schutz vor Cross-Site Request fälschungstoken (CSRF) bietet. Ursprünglich in [2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07)entworfen wurde, wurde der Entwurfs Standard in [2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00)aktualisiert. Der aktualisierte Standard ist nicht abwärts kompatibel mit dem vorherigen Standard, und es gibt folgende Unterschiede:

* Cookies ohne SameSite-Header werden `SameSite=Lax` standardmäßig als behandelt.
* `SameSite=None` muss verwendet werden, um eine standortübergreifende cookie Verwendung zu ermöglichen.
* CookieGibt an, dass Assert `SameSite=None` auch als markiert werden muss `Secure` .
* Bei Anwendungen, die verwenden, treten [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) möglicherweise Probleme mit `sameSite=Lax` oder en auf, `sameSite=Strict` cookie da `<iframe>` als standortübergreifende Szenarios behandelt wird.
* Der `SameSite=None` -Wert ist durch den [2016-Standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07) nicht zulässig und bewirkt, dass einige Implementierungen wie z. b cookie . behandeln `SameSite=Strict` . Siehe [Unterstützung älterer Browser](#sob) in diesem Dokument.

Die `SameSite=Lax` Einstellung funktioniert für die meisten Anwendungen cookie . Für einige Formen der Authentifizierung wie [OpenID Connect](https://openid.net/connect/) (oidc) und [WS-](https://auth0.com/docs/protocols/ws-fed) Verbund werden standardmäßig Post basierte Umleitungen bereitgestellt. Die Post basierten Umleitungen veranlassen den SameSite-Browserschutz, sodass SameSite für diese Komponenten deaktiviert ist. Die meisten [OAuth](https://oauth.net/) -Anmeldungen sind aufgrund von Unterschieden in der Art der Anforderungs Abläufe nicht betroffen.

Jede ASP.net Core Komponente, die cookie s ausgibt, muss entscheiden, ob SameSite geeignet ist.

## <a name="samesite-and-no-locidentity"></a>SameSite und Identity

[!INCLUDE[](~/includes/SameSiteIdentity.md)]

## <a name="samesite-test-sample-code"></a>SameSite-Testbeispiel Code

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

Die folgenden Beispiele können heruntergeladen und getestet werden:

| Beispiel               | Dokument |
| ----------------- | ------------ |
| [.Net Core MVC](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| [.Net Core- Razor Seiten](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)  | <xref:security/samesite/rp21> |

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

Das folgende Beispiel kann heruntergeladen und getestet werden:


| Beispiel               | Dokument |
| ----------------- | ------------ |
| [.Net Core- Razor Seiten](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)  | <xref:security/samesite/rp31> |

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="net-core-support-for-the-samesite-attribute"></a>.Net Core-Unterstützung für das SameSite-Attribut

.Net Core 2,2 und höhere Versionen unterstützen 2019 den Entwurf Standard für SameSite seit der Veröffentlichung von Updates im Dezember 2019. Entwickler können den Wert des SameSite-Attributs Programm gesteuert mithilfe der- `HttpCookie.SameSite` Eigenschaft steuern. Das Festlegen der- `SameSite` Eigenschaft auf Strict, Lax oder None führt dazu, dass die Werte mit dem im Netzwerk geschrieben werden cookie . Wenn Sie den Wert auf festlegen, `(SameSiteMode)(-1)` wird angegeben, dass kein SameSite-Attribut im Netzwerk mit dem cookie

[!code-csharp[](samesite/snippets/Privacy.cshtml.cs?name=snippet)]

.Net Core 3,0 und höhere Versionen unterstützen die aktualisierten SameSite-Werte und fügen der Enumeration einen zusätzlichen Enumerationswert hinzu `SameSiteMode.Unspecified` `SameSiteMode` .
Dieser neue Wert gibt an, dass keine SameSite mit dem gesendet werden soll cookie .

::: moniker-end

::: moniker range="= aspnetcore-2.1"

## <a name="december-patch-behavior-changes"></a>Änderungen im Dezember-patchverhalten

Die spezifische Behavior Change für .NET Framework und .net Core 2,1 gibt an, wie die- `SameSite` Eigenschaft den `None` Wert interpretiert. Vor dem Patch bedeutete der Wert " `None` Geben Sie das Attribut überhaupt nicht aus". nach dem Patch bedeutet dies, dass das Attribut mit dem Wert "" ausgegeben wird `None` . Nach dem Patch `SameSite` bewirkt der Wert `(SameSiteMode)(-1)` , dass das Attribut nicht ausgegeben wird.

Der Standardwert für SameSite für die Formular Authentifizierung und den Sitzungszustand cookie s wurde von `None` in geändert `Lax` .

::: moniker-end

## <a name="api-usage-with-samesite"></a>API-Nutzung mit SameSite

[HttpContext. Response. Cookie s. Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) ist standardmäßig, d. h. `Unspecified` , es wird kein SameSite-Attribut hinzugefügt, cookie und der Client verwendet sein Standardverhalten (LAX für neue Browser, keine für die alten). Der folgende Code zeigt, wie der cookie Wert von SameSite in geändert wird `SameSiteMode.Lax` :

[!code-csharp[](samesite/sample/Pages/Index.cshtml.cs?name=snippet)]

Alle ASP.net Core Komponenten, die cookie s ausgeben, überschreiben die vorangehenden Standardwerte mit Einstellungen, die für Ihre Szenarien geeignet sind Die überschriebenen vorangehenden Standardwerte haben sich nicht geändert.

| Komponente | cookie | Standard |
| ------------- | ------------- |
| <xref:Microsoft.AspNetCore.Http.CookieBuilder> | <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> | `Unspecified` |
| <xref:Microsoft.AspNetCore.Http.HttpContext.Session>  | [SessionOptions.Cookie](xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie) |`Lax` |
| <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider>  | [CookieTempdataprovideroptions.Cookie](xref:Microsoft.AspNetCore.Mvc.CookieTempDataProviderOptions.Cookie) | `Lax` |
| <xref:Microsoft.AspNetCore.Antiforgery.IAntiforgery> | [Antiforgeryoptions.Cookie](xref:Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions.Cookie)| `Strict` |
| [Cookie Genehmigung](xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*) | [CookieAuthenticationoptions.Cookie](xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.CookieName) | `Lax` |
| <xref:Microsoft.Extensions.DependencyInjection.TwitterExtensions.AddTwitter*> | [Twitteroptions. State Cookie](xref:Microsoft.AspNetCore.Authentication.Twitter.TwitterOptions.StateCookie) | `Lax`  |
| <xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [Remoteauthenticationoptions. KorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None` |
| <xref:Microsoft.Extensions.DependencyInjection.OpenIdConnectExtensions.AddOpenIdConnect*> | [Openidconnectoptions. NonceCookie](xref:Microsoft.AspNetCore.Authentication.OpenIdConnect.OpenIdConnectOptions.NonceCookie)| `None` |
| [HttpContext. Response. Cookie s. Anfügen](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) | <xref:Microsoft.AspNetCore.Http.CookieOptions> | `Unspecified` |

::: moniker range=">= aspnetcore-3.1"

ASP.net Core 3,1 und höher bietet die folgende SameSite-Unterstützung:

* Definiert das auszugebende Verhalten `SameSiteMode.None` von neu. `SameSite=None`
* Fügt einen neuen Wert `SameSiteMode.Unspecified` zum Weglassen des SameSite-Attributs hinzu.
* Alle cookie s-APIs werden standardmäßig auf eingestellt `Unspecified` . Einige Komponenten, die cookie s verwenden, legen die Werte spezifischer für Ihre Szenarios fest. Beispiele finden Sie in der obigen Tabelle.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

In ASP.net Core 3,0 und höher wurden die SameSite-Standardwerte geändert, um Konflikte mit inkonsistenten Client Standardwerten zu vermeiden. Die folgenden APIs haben den Standardwert von `SameSiteMode.Lax ` in geändert `-1` , um zu vermeiden, dass ein SameSite-Attribut für diese s ausgegeben wird cookie :

* <xref:Microsoft.AspNetCore.Http.CookieOptions> wird mit " [HttpContext. Response" verwendet. Cookie s. Anfügen](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)
* <xref:Microsoft.AspNetCore.Http.CookieBuilder>  wird als Factory für `CookieOptions`
* [CookiePolicyoptions. minimumsamesitepolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)

::: moniker-end

## <a name="history-and-changes"></a>Verlauf und Änderungen

Die SameSite-Unterstützung wurde zunächst in ASP.net Core in 2,0 mithilfe des [Entwurfs Standards 2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1)implementiert. Der 2016-Standard war Opt-in. ASP.net Core durch Festlegen mehrerer cookie s auf `Lax` standardmäßig aktiviert. Nachdem mehrere Authentifizierungs [Probleme](https://github.com/aspnet/Announcements/issues/318) aufgetreten sind, wurde die meisten SameSite-Nutzung [deaktiviert](https://github.com/aspnet/Announcements/issues/348).

Im November 2019 wurden [Patches](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) zum Aktualisieren von der 2016-Standard-auf den 2019-Standard ausgegeben. Der [2019-Entwurf der SameSite-Spezifikation](https://github.com/aspnet/Announcements/issues/390):

* Ist **nicht** abwärts kompatibel mit dem 2016-Entwurf. Weitere Informationen finden Sie [unter unterstützen älterer Browser](#sob) in diesem Dokument.
* Gibt cookie an, dass s `SameSite=Lax` Standardmäßig behandelt werden.
* Gibt cookie die an, die explizit bestätigt werden, damit `SameSite=None` die standortübergreifende Übermittlung als markiert werden kann `Secure` . `None` ist ein neuer Eintrag zum ablehnen.
* Wird von Patches unterstützt, die für ASP.net Core 2,1, 2,2 und 3,0 ausgegeben werden. ASP.net Core 3,1 bietet zusätzliche Unterstützung für SameSite.
* Ist für die standardmäßige Aktivierung durch [Chrome](https://chromestatus.com/feature/5088147346030592) in [Feb 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)vorgesehen. Die Umstellung auf diesen Standard in 2019 wurde gestartet.

## <a name="apis-impacted-by-the-change-from-the-2016-samesite-draft-standard-to-the-2019-draft-standard"></a>Von der Änderung von 2016 SameSite Draft Standard zum 2019 Draft Standard betroffene APIs

* [Http. samesitemode](xref:Microsoft.AspNetCore.Http.SameSiteMode)
* [CookieOptionen. SameSite](xref:Microsoft.AspNetCore.Http.CookieOptions.SameSite)
* [CookieBuilder. SameSite](xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite)
* [CookiePolicyoptions. minimumsamesitepolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)
* <xref:Microsoft.Net.Http.Headers.SameSiteMode?displayProperty=fullName>
* <xref:Microsoft.Net.Http.Headers.SetCookieHeaderValue.SameSite?displayProperty=fullName>

<a name="sob"></a>

## <a name="supporting-older-browsers"></a>Unterstützung älterer Browser

Der 2016 SameSite-Standard hat festgestellt, dass unbekannte Werte als Werte behandelt werden müssen `SameSite=Strict` . Apps, auf die von älteren Browsern zugegriffen wird, die den 2016 SameSite-Standard unterstützen, können unterbrechen, wenn Sie eine SameSite-Eigenschaft mit dem Wert erhalten `None` . Web-Apps müssen die Browser Erkennung implementieren, wenn Sie ältere Browser unterstützen möchten. ASP.net Core implementiert die Browser Erkennung nicht, da User-Agents Werte stark flüchtig sind und häufig geändert werden. Ein Erweiterungs Punkt in <xref:Microsoft.AspNetCore.CookiePolicy> ermöglicht das Plug in User-Agent spezifischen Logik.

`Startup.Configure`Fügen Sie in Code hinzu, der aufruft, <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> bevor aufgerufen wird, <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> oder *eine* Methode, die cookie s schreibt:

[!code-csharp[](samesite/sample/Startup.cs?name=snippet5&highlight=18-19)]

`Startup.ConfigureServices`Fügen Sie in Code ähnlich dem folgenden hinzu:

::: moniker range=">= aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup.cs?name=snippet)]

::: moniker-end

Im vorherigen Beispiel `MyUserAgentDetectionLib.DisallowsSameSiteNone` ist eine vom Benutzer bereitgestellte Bibliothek, die erkennt, ob der Benutzer-Agent SameSite nicht unterstützt `None` :

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet2)]

Der folgende Code zeigt eine Beispiel `DisallowsSameSiteNone` Methode:

> [!WARNING]
> Der folgende Code dient nur zu Demonstrationszwecken:
> * Er sollte nicht als "Fertig" betrachtet werden.
> * Er wird nicht verwaltet oder unterstützt.

[!code-csharp[](samesite/sample/Startup31.cs?name=snippetX)]

## <a name="test-apps-for-samesite-problems"></a>Testen von Apps für SameSite-Probleme

Apps, die mit Remote Standorten interagieren, z. b. durch die Anmeldung von Drittanbietern, benötigen Folgendes:

* Testen Sie die Interaktion in mehreren Browsern.
* Wenden Sie die in diesem Dokument erörterte [ Cookie Erkennung und Entschärfung des Richtlinien Browsers](#sob) an.

Testen Sie Web-Apps mithilfe einer Client Version, die das neue SameSite-Verhalten abonnieren kann. Chrome, Firefox und Chromium Edge verfügen über neue Feature-Flags, die für Tests verwendet werden können. Nachdem Ihre APP die SameSite-Patches angewendet hat, testen Sie Sie mit älteren Client Versionen, insbesondere Safari. Weitere Informationen finden Sie [unter unterstützen älterer Browser](#sob) in diesem Dokument.

### <a name="test-with-chrome"></a>Testen mit Chrome

Chrome 78 + liefert irreführende Ergebnisse, da eine vorübergehende Entschärfung vorhanden ist. Die temporäre Chrome-Minderung von 78 und höher ermöglicht es, dass cookie weniger als zwei Minuten alt sind. Chrome 76 oder 77 mit aktivierten geeigneten testflags bietet genauere Ergebnisse. Zum Testen des neuen SameSite-Verhaltens umschalten `chrome://flags/#same-site-by-default-cookies` in **aktiviert**. Ältere Versionen von Chrome (75 und niedriger) werden mit der neuen `None` Einstellung gemeldet. Siehe [Unterstützung älterer Browser](#sob) in diesem Dokument.

Google stellt keine älteren Chrome-Versionen zur Verfügung. Befolgen Sie die Anweisungen unter [Herunterladen von Chrom](https://www.chromium.org/getting-involved/download-chromium) , um ältere Versionen von Chrome zu testen. Laden Sie Chrome **nicht** von den von Ihnen bereitgestellten Links herunter, indem Sie nach älteren Versionen von Chrome suchen.

* [Chromium 76 Win64](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/664998/)
* [Chromium 74 Win64](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/638880/)

Beginnend mit der Canary-Version `80.0.3975.0` kann die vorübergehende Entschärfung von Lax und Post zu Testzwecken deaktiviert werden. dazu wird das neue Flag verwendet `--enable-features=SameSiteDefaultChecksMethodRigorously` , um das Testen von Websites und Diensten im Endzustand des Features zuzulassen, bei dem die Entschärfung entfernt wurde. Weitere Informationen finden Sie in den Informationen zu den [Software Update](https://www.chromium.org/updates/same-site) -Projekten in der Projektwebsite.

### <a name="test-with-safari"></a>Testen mit Safari

Safari 12 hat den vorherigen Entwurf streng implementiert und schlägt fehl, wenn sich der neue `None` Wert in einem befindet cookie . `None` wird über den Browser Erkennungs Code, der ältere Browser in diesem Dokument [unterstützt](#sob) , vermieden. Testen Sie Safari 12-, Safari 13-und WebKit-basierte Anmeldungen im Betriebssystem Format unter Verwendung von msal, Adal oder einer beliebigen Bibliothek, die Sie verwenden. Das Problem hängt von der zugrunde liegenden Betriebssystemversion ab. OSX-mujave (10,14) und IOS 12 haben bekanntermaßen Kompatibilitätsprobleme mit dem neuen SameSite-Verhalten. Durch ein Upgrade des Betriebssystems auf OSX Catalina (10,15) oder IOS 13 wird das Problem behoben. Safari verfügt derzeit nicht über ein Opt-in-Flag zum Testen des neuen Spezifikations Verhaltens.

### <a name="test-with-firefox"></a>Testen mit Firefox

Die Firefox-Unterstützung für den neuen Standard kann auf Version 68 + getestet werden, indem Sie sich auf der `about:config` Seite mit dem Feature-Flag anmelden `network.cookie.sameSite.laxByDefault` . Es gab keine Berichte über Kompatibilitätsprobleme mit älteren Versionen von Firefox.

### <a name="test-with-edge-browser"></a>Testen mit dem Edge-Browser

Edge unterstützt den alten SameSite-Standard. Edge Version 44 hat keine bekannten Kompatibilitätsprobleme mit dem neuen Standard.

### <a name="test-with-edge-chromium"></a>Testen mit Edge (Chrom)

SameSite-Flags werden auf der `edge://flags/#same-site-by-default-cookies` Seite festgelegt. Es wurden keine Kompatibilitätsprobleme mit Edge-Chrom erkannt.

### <a name="test-with-no-locelectron"></a>Testen mit Electron

Zu den Versionen von Electron zählen ältere Versionen von Chrom. Beispielsweise Electron ist die von Teams verwendete-Version Chrom 66, das das ältere Verhalten zeigt. Sie müssen ihre eigenen Kompatibilitätstests mit der Produktversion durchführen Electron . Weitere Informationen finden Sie [unter Unterstützung älterer Browser](#sob) im folgenden Abschnitt.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Chromium-Blog: Entwickler: machen Sie sich bereit für neue SameSite = None; Sichere Cookie Einstellungen](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)
* ["SameSite cookie s" erläutert](https://web.dev/samesite-cookies-explained/)
* [Patches vom November 2019](https://devblogs.microsoft.com/dotnet/net-core-November-2019/)

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

| Beispiel               | Dokument |
| ----------------- | ------------ |
| [.Net Core MVC](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| [.Net Core- Razor Seiten](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)  | <xref:security/samesite/rp21> |

::: moniker-end

 ::: moniker range=">= aspnetcore-3.0"

| Beispiel               | Dokument |
| ----------------- | ------------ |
| [.Net Core- Razor Seiten](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)  | <xref:security/samesite/rp31> |

::: moniker-end
