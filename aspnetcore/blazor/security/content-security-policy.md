---
title: Erzwingen einer Content Security Policy für ASP.NET Core Blazor
author: guardrex
description: Erfahren Sie, wie Sie eine Content Security Policy (CSP) mit ASP.NET Core Blazor-Apps verwenden, um beim Schutz gegen XSS-Angriffe (Cross-Site-Scripting) zu helfen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/security/content-security-policy
ms.openlocfilehash: f44f348947e31864f5d0d44c9caf1a3aa9c3b1d4
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280341"
---
# <a name="enforce-a-content-security-policy-for-aspnet-core-blazor"></a><span data-ttu-id="c4a79-103">Erzwingen einer Content Security Policy für ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="c4a79-103">Enforce a Content Security Policy for ASP.NET Core Blazor</span></span>

<span data-ttu-id="c4a79-104">Beim [Cross-Site-Scripting (XSS)](xref:security/cross-site-scripting) handelt es sich um ein Sicherheitsrisiko, bei dem ein Angreifer mindestens ein schädliches clientseitiges Skript in den gerenderten Inhalt einer App einfügt.</span><span class="sxs-lookup"><span data-stu-id="c4a79-104">[Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) is a security vulnerability where an attacker places one or more malicious client-side scripts into an app's rendered content.</span></span> <span data-ttu-id="c4a79-105">Eine Content Security Policy (CSP) hilft beim Schutz gegen XSS-Angriffe, indem sie den Browser über Folgendes informiert:</span><span class="sxs-lookup"><span data-stu-id="c4a79-105">A Content Security Policy (CSP) helps protect against XSS attacks by informing the browser of valid:</span></span>

* <span data-ttu-id="c4a79-106">gültige Quellen für geladenen Inhalt, einschließlich Skripts, Stylesheets und Bildern</span><span class="sxs-lookup"><span data-stu-id="c4a79-106">Sources for loaded content, including scripts, stylesheets, and images.</span></span>
* <span data-ttu-id="c4a79-107">gültige von einer Webseite durchgeführte Aktionen, wobei die URL-Ziele von Formularen angegeben werden</span><span class="sxs-lookup"><span data-stu-id="c4a79-107">Actions taken by a page, specifying permitted URL targets of forms.</span></span>
* <span data-ttu-id="c4a79-108">gültige Plug-Ins, die geladen werden können</span><span class="sxs-lookup"><span data-stu-id="c4a79-108">Plugins that can be loaded.</span></span>

<span data-ttu-id="c4a79-109">Zum Anwenden einer CSP auf eine App gibt der Entwickler mehrere CSP-*Anweisungen* zur Inhaltssicherheit in mindestens einem `Content-Security-Policy`-Header oder `<meta>`-Tag an.</span><span class="sxs-lookup"><span data-stu-id="c4a79-109">To apply a CSP to an app, the developer specifies several CSP content security *directives* in one or more `Content-Security-Policy` headers or `<meta>` tags.</span></span>

<span data-ttu-id="c4a79-110">Die Richtlinien werden vom Browser ausgewertet, während eine Seite geladen wird.</span><span class="sxs-lookup"><span data-stu-id="c4a79-110">Policies are evaluated by the browser while a page is loading.</span></span> <span data-ttu-id="c4a79-111">Der Browser untersucht die Quellen der Seite und bestimmt, ob diese die Anforderungen der Anweisungen zur Inhaltssicherheit erfüllen.</span><span class="sxs-lookup"><span data-stu-id="c4a79-111">The browser inspects the page's sources and determines if they meet the requirements of the content security directives.</span></span> <span data-ttu-id="c4a79-112">Wenn Richtlinienanweisungen für eine Ressource nicht erfüllt werden, lädt der Browser die Ressource nicht.</span><span class="sxs-lookup"><span data-stu-id="c4a79-112">When policy directives aren't met for a resource, the browser doesn't load the resource.</span></span> <span data-ttu-id="c4a79-113">Denken Sie sich beispielsweise eine Richtlinie, die keine Skripts von Drittanbietern zulässt.</span><span class="sxs-lookup"><span data-stu-id="c4a79-113">For example, consider a policy that doesn't allow third-party scripts.</span></span> <span data-ttu-id="c4a79-114">Wenn eine Seite ein `<script>`-Tag mit Drittanbieterursprung im `src`-Attribut enthält, verhindert der Browser das Laden des Skripts.</span><span class="sxs-lookup"><span data-stu-id="c4a79-114">When a page contains a `<script>` tag with a third-party origin in the `src` attribute, the browser prevents the script from loading.</span></span>

<span data-ttu-id="c4a79-115">CSP wird von den meisten modernen Desktopbrowsern und mobilen Browsern unterstützt, darunter Chrome, Microsoft Edge, Firefox, Opera und Safari.</span><span class="sxs-lookup"><span data-stu-id="c4a79-115">CSP is supported in most modern desktop and mobile browsers, including Chrome, Edge, Firefox, Opera, and Safari.</span></span> <span data-ttu-id="c4a79-116">CSP wird für Blazor-Apps empfohlen.</span><span class="sxs-lookup"><span data-stu-id="c4a79-116">CSP is recommended for Blazor apps.</span></span>

## <a name="policy-directives"></a><span data-ttu-id="c4a79-117">Richtlinienanweisungen</span><span class="sxs-lookup"><span data-stu-id="c4a79-117">Policy directives</span></span>

<span data-ttu-id="c4a79-118">Geben Sie mindestens die folgenden Anweisungen und Quellen für Blazor-Apps an.</span><span class="sxs-lookup"><span data-stu-id="c4a79-118">Minimally, specify the following directives and sources for Blazor apps.</span></span> <span data-ttu-id="c4a79-119">Fügen Sie nach Bedarf weitere Anweisungen und Quellen hinzu.</span><span class="sxs-lookup"><span data-stu-id="c4a79-119">Add additional directives and sources as needed.</span></span> <span data-ttu-id="c4a79-120">Die folgenden Anweisungen werden im Abschnitt [Anwenden der Richtlinie](#apply-the-policy) dieses Artikels verwendet, in dem Beispiele für Sicherheitsrichtlinien für Blazor WebAssembly und Blazor Server bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="c4a79-120">The following directives are used in the [Apply the policy](#apply-the-policy) section of this article, where example security policies for Blazor WebAssembly and Blazor Server are provided:</span></span>

* <span data-ttu-id="c4a79-121">[base-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri): Diese Anweisung schränkt die URLs für das `<base>`-Tag einer Seite ein.</span><span class="sxs-lookup"><span data-stu-id="c4a79-121">[base-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri): Restricts the URLs for a page's `<base>` tag.</span></span> <span data-ttu-id="c4a79-122">Geben Sie `self` an, um anzugeben, dass der Ursprung der App, einschließlich des Schemas und der Portnummer, eine gültige Quelle ist.</span><span class="sxs-lookup"><span data-stu-id="c4a79-122">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="c4a79-123">[block-all-mixed-content](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content): Diese Anweisung verhindert das Laden gemischter HTTP- und HTTPS-Inhalte.</span><span class="sxs-lookup"><span data-stu-id="c4a79-123">[block-all-mixed-content](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content): Prevents loading mixed HTTP and HTTPS content.</span></span>
* <span data-ttu-id="c4a79-124">[default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src): Diese Anweisung gibt ein Fallback für Quellanweisungen an, die nicht explizit von der Richtlinie angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="c4a79-124">[default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src): Indicates a fallback for source directives that aren't explicitly specified by the policy.</span></span> <span data-ttu-id="c4a79-125">Geben Sie `self` an, um anzugeben, dass der Ursprung der App, einschließlich des Schemas und der Portnummer, eine gültige Quelle ist.</span><span class="sxs-lookup"><span data-stu-id="c4a79-125">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="c4a79-126">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src): Diese Anweisung gibt gültige Quellen für Bilder an.</span><span class="sxs-lookup"><span data-stu-id="c4a79-126">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src): Indicates valid sources for images.</span></span>
  * <span data-ttu-id="c4a79-127">Geben Sie `data:` an, um das Laden von Bildern aus `data:`-URLs zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="c4a79-127">Specify `data:` to permit loading images from `data:` URLs.</span></span>
  * <span data-ttu-id="c4a79-128">Geben Sie `https:` an, um das Laden von Bildern aus HTTPS-Endpunkten zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="c4a79-128">Specify `https:` to permit loading images from HTTPS endpoints.</span></span>
* <span data-ttu-id="c4a79-129">[object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src): Diese Anweisung gibt gültige Quellen für die Tags `<object>`, `<embed>` und `<applet>` an.</span><span class="sxs-lookup"><span data-stu-id="c4a79-129">[object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src): Indicates valid sources for the `<object>`, `<embed>`, and `<applet>` tags.</span></span> <span data-ttu-id="c4a79-130">Geben Sie `none` an, um alle URL-Quellen zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="c4a79-130">Specify `none` to prevent all URL sources.</span></span>
* <span data-ttu-id="c4a79-131">[script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src): Diese Anweisung gibt gültige Quellen für Skripts an.</span><span class="sxs-lookup"><span data-stu-id="c4a79-131">[script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src): Indicates valid sources for scripts.</span></span>
  * <span data-ttu-id="c4a79-132">Geben Sie die Hostquelle `https://stackpath.bootstrapcdn.com/` für Bootstrapskripts an.</span><span class="sxs-lookup"><span data-stu-id="c4a79-132">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap scripts.</span></span>
  * <span data-ttu-id="c4a79-133">Geben Sie `self` an, um anzugeben, dass der Ursprung der App, einschließlich des Schemas und der Portnummer, eine gültige Quelle ist.</span><span class="sxs-lookup"><span data-stu-id="c4a79-133">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="c4a79-134">In einer Blazor WebAssembly-App:</span><span class="sxs-lookup"><span data-stu-id="c4a79-134">In a Blazor WebAssembly app:</span></span>
    * <span data-ttu-id="c4a79-135">Geben Sie Hashes an, um das Laden erforderlicher Skripts zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="c4a79-135">Specify hashes to permit required scripts to load.</span></span>
    * <span data-ttu-id="c4a79-136">Geben Sie `unsafe-eval` an, um `eval()` und Methoden zum Erstellen von Code aus Zeichenfolgen zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="c4a79-136">Specify `unsafe-eval` to use `eval()` and methods for creating code from strings.</span></span>
  * <span data-ttu-id="c4a79-137">Geben Sie in einer Blazor Server-App Hashes an, um das Laden erforderlicher Skripts zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="c4a79-137">In a Blazor Server app, specify hashes to permit required scripts to load.</span></span>
* <span data-ttu-id="c4a79-138">[style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src): Diese Anweisung gibt gültige Quellen für Stylesheets an.</span><span class="sxs-lookup"><span data-stu-id="c4a79-138">[style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src): Indicates valid sources for stylesheets.</span></span>
  * <span data-ttu-id="c4a79-139">Geben Sie die Hostquelle `https://stackpath.bootstrapcdn.com/` für Bootstrapstylesheets an.</span><span class="sxs-lookup"><span data-stu-id="c4a79-139">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap stylesheets.</span></span>
  * <span data-ttu-id="c4a79-140">Geben Sie `self` an, um anzugeben, dass der Ursprung der App, einschließlich des Schemas und der Portnummer, eine gültige Quelle ist.</span><span class="sxs-lookup"><span data-stu-id="c4a79-140">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="c4a79-141">Geben Sie `unsafe-inline` an, um die Verwendung von Inlineformatvorlagen zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="c4a79-141">Specify `unsafe-inline` to allow the use of inline styles.</span></span> <span data-ttu-id="c4a79-142">Die Inlinedeklaration ist für die Benutzeroberfläche in Blazor Server-Apps für die erneute Verbindung zwischen Client und Server nach der ursprünglichen Anforderung erforderlich.</span><span class="sxs-lookup"><span data-stu-id="c4a79-142">The inline declaration is required for the UI in Blazor Server apps for reconnecting the client and server after the initial request.</span></span> <span data-ttu-id="c4a79-143">In einem zukünftigen Release wird die Inlineformatierung möglicherweise entfernt, sodass `unsafe-inline` nicht mehr benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="c4a79-143">In a future release, inline styling might be removed so that `unsafe-inline` is no longer required.</span></span>
* <span data-ttu-id="c4a79-144">[upgrade-insecure-requests](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests): Diese Anweisung gibt an, dass Inhalts-URLs aus unsicheren (HTTP-) Quellen sicher über HTTPS aufgerufen werden sollen.</span><span class="sxs-lookup"><span data-stu-id="c4a79-144">[upgrade-insecure-requests](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests): Indicates that content URLs from insecure (HTTP) sources should be acquired securely over HTTPS.</span></span>

<span data-ttu-id="c4a79-145">Die vorangehenden Anweisungen werden mit Ausnahme von Microsoft Internet Explorer von allen Browsern unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c4a79-145">The preceding directives are supported by all browsers except Microsoft Internet Explorer.</span></span>

<span data-ttu-id="c4a79-146">So rufen Sie SHA-Hashes für zusätzliche Inlineskripts ab:</span><span class="sxs-lookup"><span data-stu-id="c4a79-146">To obtain SHA hashes for additional inline scripts:</span></span>

* <span data-ttu-id="c4a79-147">Wenden Sie die im Abschnitt [Anwenden der Richtlinie](#apply-the-policy) dargestellte CSP an.</span><span class="sxs-lookup"><span data-stu-id="c4a79-147">Apply the CSP shown in the [Apply the policy](#apply-the-policy) section.</span></span>
* <span data-ttu-id="c4a79-148">Greifen Sie auf die Konsole mit den Entwicklertools des Browsers zu, während Sie die App lokal ausführen.</span><span class="sxs-lookup"><span data-stu-id="c4a79-148">Access the browser's developer tools console while running the app locally.</span></span> <span data-ttu-id="c4a79-149">Der Browser berechnet Hashes für blockierte Skripts und zeigt diese an, wenn ein CSP-Header oder `meta`-Tag vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="c4a79-149">The browser calculates and displays hashes for blocked scripts when a CSP header or `meta` tag is present.</span></span>
* <span data-ttu-id="c4a79-150">Kopieren Sie die Hashes, die vom Browser bereitgestellt werden, in die `script-src`-Quellen.</span><span class="sxs-lookup"><span data-stu-id="c4a79-150">Copy the hashes provided by the browser to the `script-src` sources.</span></span> <span data-ttu-id="c4a79-151">Setzen Sie jeden Hash in einfache Anführungszeichen.</span><span class="sxs-lookup"><span data-stu-id="c4a79-151">Use single quotes around each hash.</span></span>

<span data-ttu-id="c4a79-152">Eine Browserunterstützungsmatrix für eine Content Security Policy Level 2 finden Sie unter [Can I use: Content Security Policy Level 2 (Kann ich Content Security Policy Level 2 verwenden?)](https://www.caniuse.com/#feat=contentsecuritypolicy2).</span><span class="sxs-lookup"><span data-stu-id="c4a79-152">For a Content Security Policy Level 2 browser support matrix, see [Can I use: Content Security Policy Level 2](https://www.caniuse.com/#feat=contentsecuritypolicy2).</span></span>

## <a name="apply-the-policy"></a><span data-ttu-id="c4a79-153">Anwenden der Richtlinie</span><span class="sxs-lookup"><span data-stu-id="c4a79-153">Apply the policy</span></span>

<span data-ttu-id="c4a79-154">Verwenden Sie ein `<meta>`-Tag, um die Richtlinie anzuwenden:</span><span class="sxs-lookup"><span data-stu-id="c4a79-154">Use a `<meta>` tag to apply the policy:</span></span>

* <span data-ttu-id="c4a79-155">Legen Sie den Wert des `http-equiv`-Attributs auf `Content-Security-Policy` fest.</span><span class="sxs-lookup"><span data-stu-id="c4a79-155">Set the value of the `http-equiv` attribute to `Content-Security-Policy`.</span></span>
* <span data-ttu-id="c4a79-156">Platzieren Sie die Anweisungen im Wert des `content`-Attributs.</span><span class="sxs-lookup"><span data-stu-id="c4a79-156">Place the directives in the `content` attribute value.</span></span> <span data-ttu-id="c4a79-157">Trennen Sie Anweisungen durch ein Semikolon (`;`).</span><span class="sxs-lookup"><span data-stu-id="c4a79-157">Separate directives with a semicolon (`;`).</span></span>
* <span data-ttu-id="c4a79-158">Platzieren Sie das `meta`-Tag immer im `<head>`-Inhalt.</span><span class="sxs-lookup"><span data-stu-id="c4a79-158">Always place the `meta` tag in the `<head>` content.</span></span>

<span data-ttu-id="c4a79-159">In den folgenden Abschnitten werden Beispielrichtlinien für Blazor WebAssembly und Blazor Server aufgezeigt.</span><span class="sxs-lookup"><span data-stu-id="c4a79-159">The following sections show example policies for Blazor WebAssembly and Blazor Server.</span></span> <span data-ttu-id="c4a79-160">Diese Beispiele werden für jedes Release von Blazor mit diesem Artikel versioniert.</span><span class="sxs-lookup"><span data-stu-id="c4a79-160">These examples are versioned with this article for each release of Blazor.</span></span> <span data-ttu-id="c4a79-161">Wenn Sie eine für Ihr Release geeignete Version verwenden möchten, wählen Sie die Dokumentversion mithilfe der Dropdownauswahl **Version** auf dieser Webseite aus.</span><span class="sxs-lookup"><span data-stu-id="c4a79-161">To use a version appropriate for your release, select the document version with the **Version** drop down selector on this webpage.</span></span>

### Blazor WebAssembly

<span data-ttu-id="c4a79-162">Wenden Sie im `<head>`-Inhalt der Hostseite `wwwroot/index.html` die im Abschnitt [Richtlinienanweisungen](#policy-directives) beschriebenen Anweisungen an:</span><span class="sxs-lookup"><span data-stu-id="c4a79-162">In the `<head>` content of the `wwwroot/index.html` host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

::: moniker range=">= aspnetcore-5.0"

```html
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=' 
                          'unsafe-eval';
               style-src https://stackpath.bootstrapcdn.com/
                         'self'
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

```html
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=' 
                          'sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=' 
                          'sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=' 
                          'unsafe-eval';
               style-src https://stackpath.bootstrapcdn.com/
                         'self'
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

::: moniker-end

<span data-ttu-id="c4a79-163">Fügen Sie zusätzliche `script-src`- und `style-src`-Hashes hinzu, die von der App benötigt werden.</span><span class="sxs-lookup"><span data-stu-id="c4a79-163">Add additional `script-src` and `style-src` hashes as required by the app.</span></span> <span data-ttu-id="c4a79-164">Verwenden Sie während der Entwicklung ein Onlinetool oder Browserentwicklertools, damit die Hashes für Sie berechnet werden.</span><span class="sxs-lookup"><span data-stu-id="c4a79-164">During development, use an online tool or browser developer tools to have the hashes calculated for you.</span></span> <span data-ttu-id="c4a79-165">Beispielsweise meldet der folgende Fehler einer Browsertoolkonsole den Hash für ein erforderliches Skript, das nicht durch die Richtlinie abgedeckt ist:</span><span class="sxs-lookup"><span data-stu-id="c4a79-165">For example, the following browser tools console error reports the hash for a required script not covered by the policy:</span></span>

> <span data-ttu-id="c4a79-166">Die Ausführung des Inlineskripts wurde verweigert, da es die folgende Anweisung der Inhaltssicherheitsrichtlinie verletzt: „...“.</span><span class="sxs-lookup"><span data-stu-id="c4a79-166">Refused to execute inline script because it violates the following Content Security Policy directive: " ... ".</span></span> <span data-ttu-id="c4a79-167">Zum Aktivieren der Inlineausführung ist entweder das Schlüsselwort „unsafe-inline“, ein Hash („sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=“) oder eine Nonce („nonce-...“) erforderlich.</span><span class="sxs-lookup"><span data-stu-id="c4a79-167">Either the 'unsafe-inline' keyword, a hash ('sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA='), or a nonce ('nonce-...') is required to enable inline execution.</span></span>

<span data-ttu-id="c4a79-168">Das Skript, das diesem Fehler zugeordnet ist, wird neben dem Fehler in der Konsole angezeigt.</span><span class="sxs-lookup"><span data-stu-id="c4a79-168">The particular script associated with the error is displayed in the console next to the error.</span></span>

### Blazor Server

<span data-ttu-id="c4a79-169">Wenden Sie im `<head>`-Inhalt der Hostseite `Pages/_Host.cshtml` die im Abschnitt [Richtlinienanweisungen](#policy-directives) beschriebenen Anweisungen an:</span><span class="sxs-lookup"><span data-stu-id="c4a79-169">In the `<head>` content of the `Pages/_Host.cshtml` host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

::: moniker range=">= aspnetcore-5.0"

```cshtml
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self';
               style-src https://stackpath.bootstrapcdn.com/
                         'self' 
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

```cshtml
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=';
               style-src https://stackpath.bootstrapcdn.com/
                         'self' 
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

::: moniker-end

<span data-ttu-id="c4a79-170">Fügen Sie zusätzliche `script-src`- und `style-src`-Hashes hinzu, die von der App benötigt werden.</span><span class="sxs-lookup"><span data-stu-id="c4a79-170">Add additional `script-src` and `style-src` hashes as required by the app.</span></span> <span data-ttu-id="c4a79-171">Verwenden Sie während der Entwicklung ein Onlinetool oder Browserentwicklertools, damit die Hashes für Sie berechnet werden.</span><span class="sxs-lookup"><span data-stu-id="c4a79-171">During development, use an online tool or browser developer tools to have the hashes calculated for you.</span></span> <span data-ttu-id="c4a79-172">Beispielsweise meldet der folgende Fehler einer Browsertoolkonsole den Hash für ein erforderliches Skript, das nicht durch die Richtlinie abgedeckt ist:</span><span class="sxs-lookup"><span data-stu-id="c4a79-172">For example, the following browser tools console error reports the hash for a required script not covered by the policy:</span></span>

> <span data-ttu-id="c4a79-173">Die Ausführung des Inlineskripts wurde verweigert, da es die folgende Anweisung der Inhaltssicherheitsrichtlinie verletzt: „...“.</span><span class="sxs-lookup"><span data-stu-id="c4a79-173">Refused to execute inline script because it violates the following Content Security Policy directive: " ... ".</span></span> <span data-ttu-id="c4a79-174">Zum Aktivieren der Inlineausführung ist entweder das Schlüsselwort „unsafe-inline“, ein Hash („sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=“) oder eine Nonce („nonce-...“) erforderlich.</span><span class="sxs-lookup"><span data-stu-id="c4a79-174">Either the 'unsafe-inline' keyword, a hash ('sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA='), or a nonce ('nonce-...') is required to enable inline execution.</span></span>

<span data-ttu-id="c4a79-175">Das Skript, das diesem Fehler zugeordnet ist, wird neben dem Fehler in der Konsole angezeigt.</span><span class="sxs-lookup"><span data-stu-id="c4a79-175">The particular script associated with the error is displayed in the console next to the error.</span></span>

## <a name="meta-tag-limitations"></a><span data-ttu-id="c4a79-176">Einschränkungen für META-Tags</span><span class="sxs-lookup"><span data-stu-id="c4a79-176">Meta tag limitations</span></span>

<span data-ttu-id="c4a79-177">Die folgenden Direktiven werden von einer `<meta>`-Tagrichtlinie nicht unterstützt:</span><span class="sxs-lookup"><span data-stu-id="c4a79-177">A `<meta>` tag policy doesn't support the following directives:</span></span>

* [<span data-ttu-id="c4a79-178">frame-ancestors</span><span class="sxs-lookup"><span data-stu-id="c4a79-178">frame-ancestors</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [<span data-ttu-id="c4a79-179">report-to</span><span class="sxs-lookup"><span data-stu-id="c4a79-179">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="c4a79-180">report-uri</span><span class="sxs-lookup"><span data-stu-id="c4a79-180">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [<span data-ttu-id="c4a79-181">sandbox</span><span class="sxs-lookup"><span data-stu-id="c4a79-181">sandbox</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

<span data-ttu-id="c4a79-182">Zum Unterstützen der vorangehenden Anweisung verwenden Sie einen Header mit dem Namen `Content-Security-Policy`.</span><span class="sxs-lookup"><span data-stu-id="c4a79-182">To support the preceding directives, use a header named `Content-Security-Policy`.</span></span> <span data-ttu-id="c4a79-183">Die Zeichenfolge der Anweisung ist der Wert des Headers.</span><span class="sxs-lookup"><span data-stu-id="c4a79-183">The directive string is the header's value.</span></span>

## <a name="test-a-policy-and-receive-violation-reports"></a><span data-ttu-id="c4a79-184">Testen einer Richtlinie und Empfangen von Berichten zu Verstößen</span><span class="sxs-lookup"><span data-stu-id="c4a79-184">Test a policy and receive violation reports</span></span>

<span data-ttu-id="c4a79-185">Durch das Testen kann besser sichergestellt werden, dass Drittanbieterskripts beim Erstellen einer anfänglichen Richtlinie nicht versehentlich blockiert werden.</span><span class="sxs-lookup"><span data-stu-id="c4a79-185">Testing helps confirm that third-party scripts aren't inadvertently blocked when building an initial policy.</span></span>

<span data-ttu-id="c4a79-186">Legen Sie das `<meta>`-Attribut des `http-equiv`-Tags oder den Headernamen einer headerbasierten Richtlinie auf `Content-Security-Policy-Report-Only` fest, um eine Richtlinie eine Zeit lang zu testen, ohne die Richtlinienanweisungen zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="c4a79-186">To test a policy over a period of time without enforcing the policy directives, set the `<meta>` tag's `http-equiv` attribute or header name of a header-based policy to `Content-Security-Policy-Report-Only`.</span></span> <span data-ttu-id="c4a79-187">Fehlerberichte werden als JSON-Dokumente an eine angegebene URL gesendet.</span><span class="sxs-lookup"><span data-stu-id="c4a79-187">Failure reports are sent as JSON documents to a specified URL.</span></span> <span data-ttu-id="c4a79-188">Weitere Informationen finden Sie unter [MDN Web Docs: Content-Security-Policy-Report-Only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).</span><span class="sxs-lookup"><span data-stu-id="c4a79-188">For more information, see [MDN web docs: Content-Security-Policy-Report-Only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).</span></span>

<span data-ttu-id="c4a79-189">Informationen zur Berichterstattung zu Verstößen während einer aktiven Richtlinie finden Sie in folgenden Artikeln:</span><span class="sxs-lookup"><span data-stu-id="c4a79-189">For reporting on violations while a policy is active, see the following articles:</span></span>

* [<span data-ttu-id="c4a79-190">report-to</span><span class="sxs-lookup"><span data-stu-id="c4a79-190">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="c4a79-191">report-uri</span><span class="sxs-lookup"><span data-stu-id="c4a79-191">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

<span data-ttu-id="c4a79-192">Obwohl die Verwendung von `report-uri` nicht mehr empfohlen wird, sollten beide Anweisungen verwendet werden, bis `report-to` von allen wichtigen Browsern unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="c4a79-192">Although `report-uri` is no longer recommended for use, both directives should be used until `report-to` is supported by all of the major browsers.</span></span> <span data-ttu-id="c4a79-193">Verwenden Sie nicht ausschließlich `report-uri`, da die Unterstützung für `report-uri` von Browsern *jederzeit* beendet werden könnte.</span><span class="sxs-lookup"><span data-stu-id="c4a79-193">Don't exclusively use `report-uri` because support for `report-uri` is subject to being dropped *at any time* from browsers.</span></span> <span data-ttu-id="c4a79-194">Entfernen Sie die Unterstützung für `report-uri` in Ihren Richtlinien, sobald `report-to` vollständig unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="c4a79-194">Remove support for `report-uri` in your policies when `report-to` is fully supported.</span></span> <span data-ttu-id="c4a79-195">Die Einführung von `report-to` können Sie unter [Can I use: report-to (Kann ich „report-to“ verwenden?)](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to) nachverfolgen.</span><span class="sxs-lookup"><span data-stu-id="c4a79-195">To track adoption of `report-to`, see [Can I use: report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).</span></span>

<span data-ttu-id="c4a79-196">Testen und aktualisieren Sie die Richtlinie einer App bei jedem Release.</span><span class="sxs-lookup"><span data-stu-id="c4a79-196">Test and update an app's policy every release.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="c4a79-197">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="c4a79-197">Troubleshoot</span></span>

* <span data-ttu-id="c4a79-198">Fehler werden in der Konsole mit den Entwicklertools des Browsers angezeigt.</span><span class="sxs-lookup"><span data-stu-id="c4a79-198">Errors appear in the browser's developer tools console.</span></span> <span data-ttu-id="c4a79-199">Browser stellen Informationen über Folgendes bereit:</span><span class="sxs-lookup"><span data-stu-id="c4a79-199">Browsers provide information about:</span></span>
  * <span data-ttu-id="c4a79-200">Elemente, die die Richtlinie nicht einhalten</span><span class="sxs-lookup"><span data-stu-id="c4a79-200">Elements that don't comply with the policy.</span></span>
  * <span data-ttu-id="c4a79-201">Vorgehensweise zur Anpassung der Richtlinie, sodass ein blockiertes Element zugelassen wird</span><span class="sxs-lookup"><span data-stu-id="c4a79-201">How to modify the policy to allow for a blocked item.</span></span>
* <span data-ttu-id="c4a79-202">Eine Richtlinie ist nur dann vollständig wirksam, wenn der Clientbrowser alle enthaltenen Anweisung unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c4a79-202">A policy is only completely effective when the client's browser supports all of the included directives.</span></span> <span data-ttu-id="c4a79-203">Eine aktuelle Browserunterstützungsmatrix finden Sie unter [Can I use: Content-Security-Policy (Kann ich Content-Security-Policy verwenden?)](https://caniuse.com/#search=Content-Security-Policy).</span><span class="sxs-lookup"><span data-stu-id="c4a79-203">For a current browser support matrix, see [Can I use: Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c4a79-204">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="c4a79-204">Additional resources</span></span>

* [<span data-ttu-id="c4a79-205">MDN-Webdokumentationen: Content-Security-Policy</span><span class="sxs-lookup"><span data-stu-id="c4a79-205">MDN web docs: Content-Security-Policy</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy)
* [<span data-ttu-id="c4a79-206">Content Security Policy Level 2</span><span class="sxs-lookup"><span data-stu-id="c4a79-206">Content Security Policy Level 2</span></span>](https://www.w3.org/TR/CSP2/)
* [<span data-ttu-id="c4a79-207">Google CSP Evaluator</span><span class="sxs-lookup"><span data-stu-id="c4a79-207">Google CSP Evaluator</span></span>](https://csp-evaluator.withgoogle.com/)
