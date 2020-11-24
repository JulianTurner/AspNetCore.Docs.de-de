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
ms.openlocfilehash: 744449240fabc3dae317d0d7bc9090311521c224
ms.sourcegitcommit: 1ea3f23bec63e96ffc3a927992f30a5fc0de3ff9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94570119"
---
# <a name="enforce-a-content-security-policy-for-aspnet-core-no-locblazor"></a>Erzwingen einer Content Security Policy für ASP.NET Core Blazor

Von [Javier Calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)

Beim [Cross-Site-Scripting (XSS)](xref:security/cross-site-scripting) handelt es sich um ein Sicherheitsrisiko, bei dem ein Angreifer mindestens ein schädliches clientseitiges Skript in den gerenderten Inhalt einer App einfügt. Eine Content Security Policy (CSP) hilft beim Schutz gegen XSS-Angriffe, indem sie den Browser über Folgendes informiert:

* gültige Quellen für geladenen Inhalt, einschließlich Skripts, Stylesheets und Bildern
* gültige von einer Webseite durchgeführte Aktionen, wobei die URL-Ziele von Formularen angegeben werden
* gültige Plug-Ins, die geladen werden können

Zum Anwenden einer CSP auf eine App gibt der Entwickler mehrere CSP-*Anweisungen* zur Inhaltssicherheit in mindestens einem `Content-Security-Policy`-Header oder `<meta>`-Tag an.

Die Richtlinien werden vom Browser ausgewertet, während eine Seite geladen wird. Der Browser untersucht die Quellen der Seite und bestimmt, ob diese die Anforderungen der Anweisungen zur Inhaltssicherheit erfüllen. Wenn Richtlinienanweisungen für eine Ressource nicht erfüllt werden, lädt der Browser die Ressource nicht. Denken Sie sich beispielsweise eine Richtlinie, die keine Skripts von Drittanbietern zulässt. Wenn eine Seite ein `<script>`-Tag mit Drittanbieterursprung im `src`-Attribut enthält, verhindert der Browser das Laden des Skripts.

CSP wird von den meisten modernen Desktopbrowsern und mobilen Browsern unterstützt, darunter Chrome, Microsoft Edge, Firefox, Opera und Safari. CSP wird für Blazor-Apps empfohlen.

## <a name="policy-directives"></a>Richtlinienanweisungen

Geben Sie mindestens die folgenden Anweisungen und Quellen für Blazor-Apps an. Fügen Sie nach Bedarf weitere Anweisungen und Quellen hinzu. Die folgenden Anweisungen werden im Abschnitt [Anwenden der Richtlinie](#apply-the-policy) dieses Artikels verwendet, in dem Beispiele für Sicherheitsrichtlinien für Blazor WebAssembly und Blazor Server bereitgestellt werden:

* [base-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri): Diese Anweisung schränkt die URLs für das `<base>`-Tag einer Seite ein. Geben Sie `self` an, um anzugeben, dass der Ursprung der App, einschließlich des Schemas und der Portnummer, eine gültige Quelle ist.
* [block-all-mixed-content](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content): Diese Anweisung verhindert das Laden gemischter HTTP- und HTTPS-Inhalte.
* [default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src): Diese Anweisung gibt ein Fallback für Quellanweisungen an, die nicht explizit von der Richtlinie angegeben werden. Geben Sie `self` an, um anzugeben, dass der Ursprung der App, einschließlich des Schemas und der Portnummer, eine gültige Quelle ist.
* [img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src): Diese Anweisung gibt gültige Quellen für Bilder an.
  * Geben Sie `data:` an, um das Laden von Bildern aus `data:`-URLs zuzulassen.
  * Geben Sie `https:` an, um das Laden von Bildern aus HTTPS-Endpunkten zuzulassen.
* [object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src): Diese Anweisung gibt gültige Quellen für die Tags `<object>`, `<embed>` und `<applet>` an. Geben Sie `none` an, um alle URL-Quellen zu verhindern.
* [script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src): Diese Anweisung gibt gültige Quellen für Skripts an.
  * Geben Sie die Hostquelle `https://stackpath.bootstrapcdn.com/` für Bootstrapskripts an.
  * Geben Sie `self` an, um anzugeben, dass der Ursprung der App, einschließlich des Schemas und der Portnummer, eine gültige Quelle ist.
  * In einer Blazor WebAssembly-App:
    * Geben Sie Hashes an, um das Laden erforderlicher Skripts zuzulassen.
    * Geben Sie `unsafe-eval` an, um `eval()` und Methoden zum Erstellen von Code aus Zeichenfolgen zu verwenden.
  * Geben Sie in einer Blazor Server-App Hashes an, um das Laden erforderlicher Skripts zuzulassen.
* [style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src): Diese Anweisung gibt gültige Quellen für Stylesheets an.
  * Geben Sie die Hostquelle `https://stackpath.bootstrapcdn.com/` für Bootstrapstylesheets an.
  * Geben Sie `self` an, um anzugeben, dass der Ursprung der App, einschließlich des Schemas und der Portnummer, eine gültige Quelle ist.
  * Geben Sie `unsafe-inline` an, um die Verwendung von Inlineformatvorlagen zuzulassen. Die Inlinedeklaration ist für die Benutzeroberfläche in Blazor Server-Apps für die erneute Verbindung zwischen Client und Server nach der ursprünglichen Anforderung erforderlich. In einem zukünftigen Release wird die Inlineformatierung möglicherweise entfernt, sodass `unsafe-inline` nicht mehr benötigt wird.
* [upgrade-insecure-requests](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests): Diese Anweisung gibt an, dass Inhalts-URLs aus unsicheren (HTTP-) Quellen sicher über HTTPS aufgerufen werden sollen.

Die vorangehenden Anweisungen werden mit Ausnahme von Microsoft Internet Explorer von allen Browsern unterstützt.

So rufen Sie SHA-Hashes für zusätzliche Inlineskripts ab:

* Wenden Sie die im Abschnitt [Anwenden der Richtlinie](#apply-the-policy) dargestellte CSP an.
* Greifen Sie auf die Konsole mit den Entwicklertools des Browsers zu, während Sie die App lokal ausführen. Der Browser berechnet Hashes für blockierte Skripts und zeigt diese an, wenn ein CSP-Header oder `meta`-Tag vorhanden ist.
* Kopieren Sie die Hashes, die vom Browser bereitgestellt werden, in die `script-src`-Quellen. Setzen Sie jeden Hash in einfache Anführungszeichen.

Eine Browserunterstützungsmatrix für eine Content Security Policy Level 2 finden Sie unter [Can I use: Content Security Policy Level 2 (Kann ich Content Security Policy Level 2 verwenden?)](https://www.caniuse.com/#feat=contentsecuritypolicy2).

## <a name="apply-the-policy"></a>Anwenden der Richtlinie

Verwenden Sie ein `<meta>`-Tag, um die Richtlinie anzuwenden:

* Legen Sie den Wert des `http-equiv`-Attributs auf `Content-Security-Policy` fest.
* Platzieren Sie die Anweisungen im Wert des `content`-Attributs. Trennen Sie Anweisungen durch ein Semikolon (`;`).
* Platzieren Sie das `meta`-Tag immer im `<head>`-Inhalt.

In den folgenden Abschnitten werden Beispielrichtlinien für Blazor WebAssembly und Blazor Server aufgezeigt. Diese Beispiele werden für jedes Release von Blazor mit diesem Artikel versioniert. Wenn Sie eine für Ihr Release geeignete Version verwenden möchten, wählen Sie die Dokumentversion mithilfe der Dropdownauswahl **Version** auf dieser Webseite aus.

### Blazor WebAssembly

Wenden Sie im `<head>`-Inhalt der Hostseite `wwwroot/index.html` die im Abschnitt [Richtlinienanweisungen](#policy-directives) beschriebenen Anweisungen an:

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

Fügen Sie zusätzliche `script-src`- und `style-src`-Hashes hinzu, die von der App benötigt werden. Verwenden Sie während der Entwicklung ein Onlinetool oder Browserentwicklertools, damit die Hashes für Sie berechnet werden. Beispielsweise meldet der folgende Fehler einer Browsertoolkonsole den Hash für ein erforderliches Skript, das nicht durch die Richtlinie abgedeckt ist:

> Die Ausführung des Inlineskripts wurde verweigert, da es die folgende Anweisung der Inhaltssicherheitsrichtlinie verletzt: „...“. Zum Aktivieren der Inlineausführung ist entweder das Schlüsselwort „unsafe-inline“, ein Hash („sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=“) oder eine Nonce („nonce-...“) erforderlich.

Das Skript, das diesem Fehler zugeordnet ist, wird neben dem Fehler in der Konsole angezeigt.

### Blazor Server

Wenden Sie im `<head>`-Inhalt der Hostseite `Pages/_Host.cshtml` die im Abschnitt [Richtlinienanweisungen](#policy-directives) beschriebenen Anweisungen an:

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

Fügen Sie zusätzliche `script-src`- und `style-src`-Hashes hinzu, die von der App benötigt werden. Verwenden Sie während der Entwicklung ein Onlinetool oder Browserentwicklertools, damit die Hashes für Sie berechnet werden. Beispielsweise meldet der folgende Fehler einer Browsertoolkonsole den Hash für ein erforderliches Skript, das nicht durch die Richtlinie abgedeckt ist:

> Die Ausführung des Inlineskripts wurde verweigert, da es die folgende Anweisung der Inhaltssicherheitsrichtlinie verletzt: „...“. Zum Aktivieren der Inlineausführung ist entweder das Schlüsselwort „unsafe-inline“, ein Hash („sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=“) oder eine Nonce („nonce-...“) erforderlich.

Das Skript, das diesem Fehler zugeordnet ist, wird neben dem Fehler in der Konsole angezeigt.

## <a name="meta-tag-limitations"></a>Einschränkungen für META-Tags

Die folgenden Direktiven werden von einer `<meta>`-Tagrichtlinie nicht unterstützt:

* [frame-ancestors](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [report-to](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [report-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [sandbox](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

Zum Unterstützen der vorangehenden Anweisung verwenden Sie einen Header mit dem Namen `Content-Security-Policy`. Die Zeichenfolge der Anweisung ist der Wert des Headers.

## <a name="test-a-policy-and-receive-violation-reports"></a>Testen einer Richtlinie und Empfangen von Berichten zu Verstößen

Durch das Testen kann besser sichergestellt werden, dass Drittanbieterskripts beim Erstellen einer anfänglichen Richtlinie nicht versehentlich blockiert werden.

Legen Sie das `<meta>`-Attribut des `http-equiv`-Tags oder den Headernamen einer headerbasierten Richtlinie auf `Content-Security-Policy-Report-Only` fest, um eine Richtlinie eine Zeit lang zu testen, ohne die Richtlinienanweisungen zu erzwingen. Fehlerberichte werden als JSON-Dokumente an eine angegebene URL gesendet. Weitere Informationen finden Sie unter [MDN Web Docs: Content-Security-Policy-Report-Only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).

Informationen zur Berichterstattung zu Verstößen während einer aktiven Richtlinie finden Sie in folgenden Artikeln:

* [report-to](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [report-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

Obwohl die Verwendung von `report-uri` nicht mehr empfohlen wird, sollten beide Anweisungen verwendet werden, bis `report-to` von allen wichtigen Browsern unterstützt wird. Verwenden Sie nicht ausschließlich `report-uri`, da die Unterstützung für `report-uri` von Browsern *jederzeit* beendet werden könnte. Entfernen Sie die Unterstützung für `report-uri` in Ihren Richtlinien, sobald `report-to` vollständig unterstützt wird. Die Einführung von `report-to` können Sie unter [Can I use: report-to (Kann ich „report-to“ verwenden?)](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to) nachverfolgen.

Testen und aktualisieren Sie die Richtlinie einer App bei jedem Release.

## <a name="troubleshoot"></a>Problembehandlung

* Fehler werden in der Konsole mit den Entwicklertools des Browsers angezeigt. Browser stellen Informationen über Folgendes bereit:
  * Elemente, die die Richtlinie nicht einhalten
  * Vorgehensweise zur Anpassung der Richtlinie, sodass ein blockiertes Element zugelassen wird
* Eine Richtlinie ist nur dann vollständig wirksam, wenn der Clientbrowser alle enthaltenen Anweisung unterstützt. Eine aktuelle Browserunterstützungsmatrix finden Sie unter [Can I use: Content-Security-Policy (Kann ich Content-Security-Policy verwenden?)](https://caniuse.com/#search=Content-Security-Policy).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [MDN-Webdokumentationen: Content-Security-Policy](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy)
* [Content Security Policy Level 2](https://www.w3.org/TR/CSP2/)
* [Google CSP Evaluator](https://csp-evaluator.withgoogle.com/)
