---
title: Verhindern von Angriffen für Website übergreifende Anforderungs Fälschung (XSRF/CSRF) in ASP.net Core
author: steve-smith
description: Erfahren Sie, wie Sie Angriffe auf Web-Apps verhindern, bei denen eine böswillige Website die Interaktion zwischen einem Client Browser und der APP beeinflussen kann.
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 12/05/2019
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
uid: security/anti-request-forgery
ms.openlocfilehash: 3bb3c059eafa8e948fe2e719207927c009902e59
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057446"
---
# <a name="prevent-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a>Verhindern von Angriffen für Website übergreifende Anforderungs Fälschung (XSRF/CSRF) in ASP.net Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT), von " [fyaz Hasan](https://twitter.com/FiyazBinHasan)" und [Steve Smith](https://ardalis.com/)

Website übergreifende Anforderungs Fälschung (auch als XSRF oder CSRF bezeichnet) ist ein Angriff auf im Web gehostete Apps, bei dem eine böswillige Web-App die Interaktion zwischen einem Client Browser und einer Web-App beeinflussen kann, die diesem Browser vertraut. Diese Angriffe sind möglich, da Webbrowser einige Arten von Authentifizierungs Token automatisch mit jeder Anforderung an eine Website senden. Diese Art von Exploit wird auch als *One-Click-Angriff* oder *Sitzungsart* bezeichnet, da der Angriff die zuvor authentifizierte Sitzung des Benutzers nutzt.

Ein Beispiel für einen CSRF-Angriff:

1. Ein Benutzer meldet sich `www.good-banking-site.com` mit der Formular Authentifizierung an. Der Server authentifiziert den Benutzer und gibt eine Antwort aus, die eine Authentifizierung enthält cookie . Die Site ist anfällig für Angriffe, da Sie jede empfangene Anforderung mit einer gültigen Authentifizierung vertraut cookie .
1. Der Benutzer besucht eine böswillige Website, `www.bad-crook-site.com` .

   Die böswillige Site `www.bad-crook-site.com` enthält ein HTML-Formular, das dem folgenden ähnelt:

   ```html
   <h1>Congratulations! You're a Winner!</h1>
   <form action="http://good-banking-site.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw">
       <input type="hidden" name="Amount" value="1000000">
       <input type="submit" value="Click to collect your prize!">
   </form>
   ```

   Beachten Sie, dass das Formular die `action` Beiträge an die anfällige Site und nicht an die böswillige Site sendet. Dies ist der "Cross-Site"-Teil von CSRF.

1. Der Benutzer wählt die Schaltfläche Senden aus. Der Browser stellt die Anforderung her und schließt automatisch die Authentifizierung cookie für die angeforderte Domäne ein `www.good-banking-site.com` .
1. Die Anforderung wird auf dem `www.good-banking-site.com` Server mit dem Authentifizierungs Kontext des Benutzers ausgeführt und kann alle Aktionen ausführen, die ein authentifizierter Benutzer ausführen darf.

Zusätzlich zu dem Szenario, in dem der Benutzer die Schaltfläche zum Senden des Formulars auswählt, könnte die böswillige Site folgende Aktionen ausführen:

* Führen Sie ein Skript aus, das das Formular automatisch sendet.
* Senden Sie die Formular Übermittlung als AJAX-Anforderung.
* Blenden Sie das Formular mit CSS aus.

Für diese alternativen Szenarien sind keine Aktionen oder Eingaben vom Benutzer erforderlich, außer wenn Sie zunächst die böswillige Website besuchen.

Durch die Verwendung von HTTPS wird kein CSRF-Angriff verhindert. Die böswillige Site kann eine `https://www.good-banking-site.com/` Anforderung genauso einfach senden, wie Sie eine unsichere Anforderung senden kann.

Einige Angriffe zielen auf Endpunkte ab, die auf Get-Anforderungen reagieren. in diesem Fall kann ein imagetag verwendet werden, um die Aktion auszuführen. Diese Art von Angriff erfolgt häufig auf Forums Websites, die Bilder zulassen, aber JavaScript blockieren. Apps, die den Status von Get-Anforderungen ändern, bei denen Variablen oder Ressourcen geändert werden, sind anfällig für böswillige Angriffe. **Get-Anforderungen, die den Zustand ändern, sind unsicher. Eine bewährte Vorgehensweise besteht darin, den Status einer GET-Anforderung nie zu ändern.**

Csrf-Angriffe sind für Web-Apps möglich, die cookie für die Authentifizierung s verwenden:

* Browser speichern die cookie von einer Web-App ausgestellten.
* Gespeicherte cookie s enthalten Sitzungs- cookie e für authentifizierte Benutzer.
* Browser senden alle cookie e, die einer Domäne zugeordnet sind, an die Web-App jede Anforderung, unabhängig davon, wie die App-Anforderung innerhalb des Browsers generiert wurde.

Allerdings sind CSRF-Angriffe nicht auf das Ausnutzen von cookie s beschränkt. Beispielsweise sind die Standard-und Digestauthentifizierung auch anfällig. Nachdem sich ein Benutzer mit der Standard-oder Digestauthentifizierung angemeldet hat, sendet der Browser die Anmelde Informationen automatisch, bis die Sitzung &dagger; beendet wird.

&dagger;In diesem Kontext bezieht sich *Sitzung* auf die Client seitige Sitzung, in der der Benutzer authentifiziert wird. Sie steht nicht im Zusammenhang mit serverseitigen Sitzungen oder [ASP.net Core Sitzungs Middleware](xref:fundamentals/app-state).

Benutzer können Schutz vor CSRF-Sicherheitsrisiken durch Vorkehrungen treffen:

* Melden Sie sich von Web-Apps ab, wenn Sie diese verwenden.
* Browser cookie s regelmäßig löschen.

Csrf-Sicherheitsrisiken sind jedoch im Grunde ein Problem mit der Web-App, nicht mit dem Endbenutzer.

## <a name="authentication-fundamentals"></a>Grundlagen der Authentifizierung

Cookiedie-basierte Authentifizierung ist eine beliebte Form der Authentifizierung. Tokenbasierte Authentifizierungssysteme werden immer beliebter, insbesondere für Single-Page-Anwendungen (Spas).

### <a name="no-loccookie-based-authentication"></a>Cookie-basierte Authentifizierung

Wenn ein Benutzer sich mit seinem Benutzernamen und Kennwort authentifiziert, wird ein Token ausgestellt, das ein Authentifizierungs Ticket enthält, das für die Authentifizierung und Autorisierung verwendet werden kann. Das Token wird als gespeichert cookie , das jede vom Client vornimmt. Die Erstellung und Überprüfung dieses Vorgangs cookie erfolgt durch die Cookie Authentifizierungs Middleware. Die [Middleware](xref:fundamentals/middleware/index) serialisiert einen Benutzer Prinzipal in einen verschlüsselten cookie . Bei nachfolgenden Anforderungen überprüft die Middleware den cookie , erstellt den Prinzipal neu und weist den Prinzipal der [User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) -Eigenschaft von [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext)zu.

### <a name="token-based-authentication"></a>Tokenbasierte Authentifizierung

Wenn ein Benutzer authentifiziert wird, wird ein Token ausgestellt (kein antifälschungstoken). Das Token enthält Benutzerinformationen in Form von [Ansprüchen](/dotnet/framework/security/claims-based-identity-model) oder ein Verweis Token, das die APP auf den Benutzer Zustand verweist, der in der APP verwaltet wird. Wenn ein Benutzer versucht, auf eine Ressource zuzugreifen, die eine Authentifizierung erfordert, wird das Token mit einem zusätzlichen Autorisierungs Header als bearertoken an die APP gesendet. Dadurch wird die APP zustandslos. In jeder nachfolgenden Anforderung wird das Token in der Anforderung für die serverseitige Validierung übermittelt. Dieses Token ist nicht *verschlüsselt*. Sie ist *codiert*. Auf dem Server wird das Token decodiert, um auf seine Informationen zuzugreifen. Um das Token für nachfolgende Anforderungen zu senden, speichern Sie das Token im lokalen Speicher des Browsers. Machen Sie sich keine Gedanken über das Sicherheitsrisiko von CSRF, wenn das Token im lokalen Speicher des Browsers gespeichert wird. Csrf ist ein Problem, wenn das Token in einer gespeichert wird cookie . Weitere Informationen finden Sie im GitHub Issue [Spa-Codebeispiel für das Hinzufügen von zwei cookie s](https://github.com/dotnet/AspNetCore.Docs/issues/13369).

### <a name="multiple-apps-hosted-at-one-domain"></a>Mehrere apps, die in einer Domäne gehostet werden

Freigegebene Hostingumgebungen sind anfällig für Session Hijacking, Login CSRF und andere Angriffe.

Obwohl `example1.contoso.net` `example2.contoso.net` es sich bei und um unterschiedliche Hosts handelt, gibt es eine implizite Vertrauensstellung zwischen den Hosts in der `*.contoso.net` Domäne. Diese implizite Vertrauensstellung ermöglicht möglicherweise nicht vertrauenswürdige Hosts, sich gegenseitig zu beeinflussen cookie (die gleichen Ursprungs Richtlinien, die die Regeln von AJAX-Anforderungen Regeln, gelten nicht unbedingt für HTTP cookie s).

Angriffe, die vertrauenswürdige cookie s zwischen apps ausnutzen, die in derselben Domäne gehostet werden, können durch das Freigeben von Domänen verhindert werden. Wenn jede app in ihrer eigenen Domäne gehostet wird, gibt es keine implizite cookie Vertrauensstellung für die Ausnutzung.

## <a name="aspnet-core-antiforgery-configuration"></a>Konfiguration der ASP.net Core-Antifälschung

> [!WARNING]
> ASP.net Core implementiert Antifälschung mithilfe [ASP.net Core Datenschutzes](xref:security/data-protection/introduction). Der Stapel für den Datenschutz muss so konfiguriert sein, dass er in einer Serverfarm funktioniert. Weitere Informationen finden Sie unter [Konfigurieren des Schutzes von Daten](xref:security/data-protection/configuration/overview) .

::: moniker range=">= aspnetcore-3.0"

Die Middleware für die Antifälschung wird dem Container für die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) hinzugefügt, wenn eine der folgenden APIs in aufgerufen wird `Startup.ConfigureServices` :

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>
* <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*>
* <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>
* <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Antifälschungsmiddleware wird dem Container für die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) hinzugefügt, wenn <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> in aufgerufen wird. `Startup.ConfigureServices`

::: moniker-end

In ASP.net Core 2,0 oder höher fügt [formtaghelper](xref:mvc/views/working-with-forms#the-form-tag-helper) antifälschungstoken in HTML-Formularelemente ein. Das folgende Markup in einer Razor Datei generiert automatisch antifälschungstokentoken:

```cshtml
<form method="post">
    ...
</form>
```

Ebenso generiert [ihtmlhelper. BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) standardmäßig antifälschungstokentoken, wenn die-Methode des Formulars nicht erhalten wird.

Die automatische Generierung von antifälschungstoken für HTML-Formularelemente tritt auf, wenn das `<form>` -Tag das `method="post"` -Attribut enthält und eine der folgenden Optionen zutrifft:

* Das Aktions Attribut ist leer ( `action=""` ).
* Das action-Attribut wird nicht bereitgestellt ( `<form method="post">` ).

Die automatische Generierung von antifälschungstoken für HTML-Formularelemente kann deaktiviert werden:

* Deaktivieren Sie antifälschungstoken explizit mit dem- `asp-antiforgery` Attribut:

  ```cshtml
  <form method="post" asp-antiforgery="false">
      ...
  </form>
  ```

* Das Formular Element wird von taghilfsprogrammen mithilfe des taghilfsprogramms [! Opt-out-Symbols](xref:mvc/views/tag-helpers/intro#opt-out)deaktiviert:

  ```cshtml
  <!form method="post">
      ...
  </!form>
  ```

* Entfernen Sie `FormTagHelper` aus der Ansicht. Der `FormTagHelper` kann aus einer Ansicht entfernt werden, indem der Ansicht die folgende-Direktive hinzugefügt wird Razor :

  ```cshtml
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> [ Razor Seiten](xref:razor-pages/index) werden automatisch vor XSRF/CSRF geschützt. Weitere Informationen finden Sie unter [XSRF/CSRF und Razor Seiten](xref:razor-pages/index#xsrf).

Der häufigste Ansatz für die Verteidigung gegen CSRF-Angriffe ist die Verwendung des *Synchronisierungs Token-Musters* (STP). STP wird verwendet, wenn der Benutzer eine Seite mit Formulardaten anfordert:

1. Der Server sendet ein Token, das der Identität des aktuellen Benutzers zugeordnet ist, an den Client.
1. Der Client sendet das Token zur Überprüfung an den Server zurück.
1. Wenn der Server ein Token empfängt, das nicht mit der Identität des authentifizierten Benutzers identisch ist, wird die Anforderung abgelehnt.

Das Token ist eindeutig und unvorhersehbar. Das Token kann auch verwendet werden, um eine ordnungsgemäße Sequenzierung einer Reihe von Anforderungen sicherzustellen (z. b. zur Sicherstellung der Anforderungs Sequenz von: Seite 1 > Seite 2 > Seite 3). Alle Formulare in ASP.net Core MVC-und Razor Seitenvorlagen generieren antifälschungstokentoken. Das folgende Paar von Sicht Beispielen generiert antifälschungstokentoken:

```cshtml
<form asp-controller="Manage" asp-action="ChangePassword" method="post">
    ...
</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    ...
}
```

Fügen Sie einem-Element explizit ein antifälschungstoken hinzu, `<form>` ohne taghilfsprogramme mit der HTML-Hilfe zu verwenden [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken) :

```cshtml
<form action="/" method="post">
    @Html.AntiForgeryToken()
</form>
```

In jedem der vorangegangenen Fälle fügt ASP.net Core ein ausgeblendetes Formularfeld ähnlich dem folgenden hinzu:

```cshtml
<input name="__RequestVerificationToken" type="hidden" value="CfDJ8NrAkS ... s2-m9Yw">
```

ASP.net Core enthält drei [Filter](xref:mvc/controllers/filters) zum Arbeiten mit antifälschungstoken:

* [ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)
* [Autovalidateantiforgerytoken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)
* [Ignoreantiforgerytoken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)

## <a name="antiforgery-options"></a>Antifälschungsoptionen

Optionen für die [Antifälschung](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) anpassen in `Startup.ConfigureServices` :

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    // Set Cookie properties using CookieBuilder properties†.
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.SuppressXFrameOptionsHeader = false;
});
```

&dagger;Legen Sie die Eigenschaften für die Antifälschung `Cookie` mithilfe der Eigenschaften der [ Cookie Builder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) -Klasse fest.

| Option | BESCHREIBUNG |
| ------ | ----------- |
| [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | Legt die Einstellungen fest, die zum Erstellen der Antifälschung s verwendet werden cookie . |
| [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | Der Name des ausgeblendeten Formular Felds, das vom antifälschungs System zum Rendering von antifälschungstoken in Sichten verwendet wird. |
| [Header Name](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | Der Name des Headers, der vom antifälschungs System verwendet wird. Gibt an `null` , dass das System nur Formulardaten berücksichtigt. |
| [Suppressxframeoptionsheader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | Gibt an, ob die Generierung des Headers unterdrückt werden soll `X-Frame-Options` . Standardmäßig wird der-Header mit dem Wert "sameorigin" generiert. Wird standardmäßig auf `false` festgelegt. |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    options.CookieDomain = "contoso.com";
    options.CookieName = "X-CSRF-TOKEN-COOKIENAME";
    options.CookiePath = "Path";
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.RequireSsl = false;
    options.SuppressXFrameOptionsHeader = false;
});
```

| Option | BESCHREIBUNG |
| ------ | ----------- |
| [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | Legt die Einstellungen fest, die zum Erstellen der Antifälschung s verwendet werden cookie . |
| [CookieDomain](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain) | Die Domäne des cookie . Wird standardmäßig auf `null` festgelegt. Diese Eigenschaft ist veraltet und wird in einer zukünftigen Version entfernt. Die empfohlene Alternative ist Cookie . -. |
| [CookieBenennen](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename) | Der Name von cookie. Wenn nicht festgelegt, generiert das System einen eindeutigen Namen, beginnend mit dem [Standard Cookie Präfix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (". Aspnetcore. Antifälschung. "). Diese Eigenschaft ist veraltet und wird in einer zukünftigen Version entfernt. Die empfohlene Alternative ist Cookie . Benennen. |
| [CookiePfad](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath) | Der Pfad, der auf dem festgelegt ist cookie . Diese Eigenschaft ist veraltet und wird in einer zukünftigen Version entfernt. Die empfohlene Alternative ist Cookie . ADS. |
| [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | Der Name des ausgeblendeten Formular Felds, das vom antifälschungs System zum Rendering von antifälschungstoken in Sichten verwendet wird. |
| [Header Name](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | Der Name des Headers, der vom antifälschungs System verwendet wird. Gibt an `null` , dass das System nur Formulardaten berücksichtigt. |
| [RequireSsl](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | Gibt an, ob HTTPS für das antifälschungs System erforderlich ist. Gibt an `true` , dass nicht-HTTPS-Anforderungen fehlschlagen. Wird standardmäßig auf `false` festgelegt. Diese Eigenschaft ist veraltet und wird in einer zukünftigen Version entfernt. Die empfohlene Alternative besteht darin, festzulegen Cookie . Securepolicy. |
| [Suppressxframeoptionsheader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | Gibt an, ob die Generierung des Headers unterdrückt werden soll `X-Frame-Options` . Standardmäßig wird der-Header mit dem Wert "sameorigin" generiert. Wird standardmäßig auf `false` festgelegt. |

::: moniker-end

Weitere Informationen finden Sie unter [ Cookie authenticationoptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).

## <a name="configure-antiforgery-features-with-iantiforgery"></a>Konfigurieren von antifälschungsfeatures mit iantifälschung

[Iantifälschung](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) bietet die API zum Konfigurieren von antifälschungs Features. `IAntiforgery` kann in der- `Configure` Methode der-Klasse angefordert werden `Startup` . Im folgenden Beispiel wird die Middleware auf der Startseite der APP verwendet, um ein antifälschungstoken zu generieren und in der Antwort als zu senden cookie (mit der standardmäßigen Angular-Benennungs Konvention, die weiter unten in diesem Thema beschrieben wird):

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
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}
```

### <a name="require-antiforgery-validation"></a>Antifälschungs Überprüfung erforderlich

[Validateantiforgerytoken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) ist ein Aktionsfilter, der auf eine einzelne Aktion, einen Controller oder Global angewendet werden kann. Anforderungen an Aktionen, die diesen Filter angewendet haben, werden blockiert, es sei denn, die Anforderung enthält ein gültiges antifälschungstoken.

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

Das- `ValidateAntiForgeryToken` Attribut erfordert ein Token für Anforderungen an die Aktionsmethoden, die es markiert, einschließlich HTTP GET-Anforderungen. Wenn das `ValidateAntiForgeryToken` -Attribut auf die Controller der APP angewendet wird, kann es mit dem-Attribut überschrieben werden `IgnoreAntiforgeryToken` .

> [!NOTE]
> ASP.net Core unterstützt das Hinzufügen von antifälschungstoken, um Anforderungen automatisch zu erhalten

### <a name="automatically-validate-antiforgery-tokens-for-unsafe-http-methods-only"></a>Antifälschungstokentoken nur für unsichere HTTP-Methoden automatisch überprüfen

ASP.net Core apps generieren keine antifälschungstokentoken für sichere HTTP-Methoden (Get, Head, Options und Trace). Anstatt das Attribut weitgehend anzuwenden `ValidateAntiForgeryToken` und dann mit Attributen zu überschreiben `IgnoreAntiforgeryToken` , kann das [autovalidateantiforgerytoken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) -Attribut verwendet werden. Dieses Attribut funktioniert identisch mit dem- `ValidateAntiForgeryToken` Attribut, mit dem Unterschied, dass es keine Token für Anforderungen erfordert, die mit den folgenden HTTP-Methoden ausgeführt werden:

* GET
* HEAD
* OPTIONS
* TRACE

Wir empfehlen die Verwendung von `AutoValidateAntiforgeryToken` für nicht-API-Szenarien. Dadurch wird sichergestellt, dass Post-Aktionen standardmäßig geschützt werden. Die Alternative besteht darin, antifälschungstokentoken standardmäßig zu ignorieren, es sei denn `ValidateAntiForgeryToken` , wird auf einzelne Aktionsmethoden angewendet. In diesem Szenario ist es wahrscheinlicher, dass eine Post-Aktionsmethode versehentlich ungeschützt bleibt, und die APP ist anfällig für CSRF-Angriffe. Alle Beiträge sollten das antifälschungstoken senden.

APIs verfügen nicht über einen automatischen Mechanismus zum Senden des nicht- cookie Teils des Tokens. Die Implementierung hängt wahrscheinlich von der Implementierung des Client Codes ab. Einige Beispiele sind unten dargestellt:

Beispiel auf Klassenebene:

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
```

Globales Beispiel:

::: moniker range="< aspnetcore-3.0"

Betreuung. Addmvc (Optionen => Optionen. Filters. Add (neues autovalidateantiforgerytokenattribute ()));

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

```csharp
services.AddControllersWithViews(options =>
    options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));
```

::: moniker-end

### <a name="override-global-or-controller-antiforgery-attributes"></a>Globale oder Controller-antifälschungsattribute überschreiben

Der [ignoreantiforgerytoken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) -Filter wird verwendet, um den Bedarf an einem antifälschungstoken für eine bestimmte Aktion (oder einen Controller) auszuschließen. Wenn dieser Filter angewendet wird, überschreibt dieser Filter die `ValidateAntiForgeryToken` Filter und Filter, die `AutoValidateAntiforgeryToken` auf einer höheren Ebene (Global oder auf einem Controller) angegeben sind.

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

## <a name="refresh-tokens-after-authentication"></a>Token nach der Authentifizierung aktualisieren

Token sollten aktualisiert werden, nachdem der Benutzer authentifiziert wurde, indem er den Benutzer auf eine Seite oder eine Razor Seiten Seite umleitet.

## <a name="javascript-ajax-and-spas"></a>JavaScript, AJAX und Spas

In herkömmlichen HTML-basierten apps werden antifälschungstokentoken mithilfe von ausgeblendeten Formularfeldern an den Server übermittelt. In modernen JavaScript-basierten apps und Spas werden viele Anforderungen Programm gesteuert durchgeführt. Diese AJAX-Anforderungen können andere Techniken (z. b. Anforderungs Header oder cookie e) verwenden, um das Token zu senden.

Wenn cookie e zum Speichern von Authentifizierungs Token und zum Authentifizieren von API-Anforderungen auf dem Server verwendet werden, ist CSRF ein potenzielles Problem. Wenn lokaler Speicher zum Speichern des Tokens verwendet wird, wird die CSRF-Sicherheits Anfälligkeit möglicherweise verringert, da Werte aus lokalem Speicher nicht automatisch mit jeder Anforderung an den Server gesendet werden. Daher ist die Verwendung von lokalem Speicher, um das antifälschungstoken auf dem Client zu speichern und das Token als Anforderungs Header zu senden, eine empfohlene Vorgehensweise.

### <a name="javascript"></a>JavaScript

Mithilfe von JavaScript mit Sichten kann das Token mithilfe eines Diensts innerhalb der Sicht erstellt werden. Fügen Sie den Dienst " [Microsoft. aspnetcore. Antifälschung. iantifälschung](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) " in die Ansicht ein, und nennen Sie " [getandstoretokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens)":

[!code-cshtml[](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,12-13,35-36)]

Bei dieser Vorgehensweise entfällt die Notwendigkeit, direkt mit dem Einrichten cookie von s vom Server oder dem Lesen aus dem Client umzugehen.

Im vorangehenden Beispiel wird JavaScript verwendet, um den Wert des ausgeblendeten Felds für den AJAX-Post-Header

JavaScript kann auch auf Token in cookie s zugreifen und den Inhalt des s verwenden cookie , um einen Header mit dem Wert des Tokens zu erstellen.

```csharp
context.Response.Cookies.Append("CSRF-TOKEN", tokens.RequestToken, 
    new Microsoft.AspNetCore.Http.CookieOptions { HttpOnly = false });
```

Wenn das Skript anfordert, das Token in einem Header mit dem Namen zu senden `X-CSRF-TOKEN` , konfigurieren Sie den antifälschungs Dienst so, dass er nach dem `X-CSRF-TOKEN` Header sucht:

```csharp
services.AddAntiforgery(options => options.HeaderName = "X-CSRF-TOKEN");
```

Im folgenden Beispiel wird JavaScript verwendet, um eine AJAX-Anforderung mit dem entsprechenden Header zu erstellen:

```javascript
function getCookie(cname) {
    var name = cname + "=";
    var decodedCookie = decodeURIComponent(document.cookie);
    var ca = decodedCookie.split(';');
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

var csrfToken = getCookie("CSRF-TOKEN");

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

### <a name="angularjs"></a>AngularJS

Angularjs verwendet eine Konvention, um CSRF zu adressieren. Wenn der Server einen cookie mit dem Namen sendet `XSRF-TOKEN` , fügt der angularjs- `$http` Dienst den cookie Wert einem Header hinzu, wenn er eine Anforderung an den Server sendet. Dieser Prozess erfolgt automatisch. Der-Header muss nicht explizit im Client festgelegt werden. Der Header Name ist `X-XSRF-TOKEN` . Der Server sollte diesen Header erkennen und seinen Inhalt überprüfen.

Damit ASP.net Core-API mit dieser Konvention beim Starten der Anwendung funktioniert:

* Konfigurieren Sie Ihre APP, um ein Token in einem cookie aufgerufenen bereitzustellen `XSRF-TOKEN` .
* Konfigurieren Sie den antifälschungs Dienst für die Suche nach einem Header mit dem Namen `X-XSRF-TOKEN` .

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
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
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

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="windows-authentication-and-antiforgery-no-loccookies"></a>Windows-Authentifizierung und- cookie Antifälschung

Wenn Sie die Windows-Authentifizierung verwenden, müssen Anwendungs Endpunkte auf die gleiche Weise wie für s vor CSRF-Angriffen geschützt werden cookie .  Der Browser sendet den Authentifizierungs Kontext implizit an den Server, weshalb Endpunkte vor CSRF-Angriffen geschützt werden müssen.

## <a name="extend-antiforgery"></a>Antifälschung erweitern

Der [iantiforgeryadditionaldataprovider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) -Typ ermöglicht es Entwicklern, das Verhalten des Anti-CSRF-Systems zu erweitern, indem zusätzliche Daten in jedem Token abgerundet werden. Die [getadditionaldata](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) -Methode wird jedes Mal aufgerufen, wenn ein Feld Token generiert wird, und der Rückgabewert wird in das generierte Token eingebettet. Ein Implementierer könnte einen Zeitstempel, eine Nonce oder einen anderen Wert zurückgeben und dann [validateadditionaldata](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) aufzurufen, um diese Daten zu validieren, wenn das Token überprüft wird. Der Benutzername des Clients ist bereits in die generierten Token eingebettet, sodass es nicht erforderlich ist, diese Informationen einzubeziehen. Wenn ein Token zusätzliche Daten enthält, aber keine `IAntiForgeryAdditionalDataProvider` konfiguriert ist, werden die ergänzenden Daten nicht überprüft.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) für das [Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (OWASP).
* <xref:host-and-deploy/web-farm>
