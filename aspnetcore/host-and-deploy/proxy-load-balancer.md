---
title: Konfigurieren von ASP.NET Core zur Verwendung mit Proxyservern und Lastenausgleich
author: rick-anderson
description: Informationen zur Konfiguration von hinter Proxyservern und Lastenausgleichsmodulen gehosteten Apps, wobei wichtige Anforderungsinformationen häufig verdeckt werden
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/proxy-load-balancer
ms.openlocfilehash: 461f6d2105d38c5dbea2f8cf479e027c2edede14
ms.sourcegitcommit: 59d95a9106301d5ec5c9f612600903a69c4580ef
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96024941"
---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a>Konfigurieren von ASP.NET Core zur Verwendung mit Proxyservern und Lastenausgleich

Von [Chris Ross](https://github.com/Tratcher)

::: moniker range=">= aspnetcore-3.0"

In der für ASP.NET Core empfohlenen Konfiguration wird die App mit dem IIS/ASP.NET Core-Modul, Nginx oder Apache gehostet. Proxyserver, Lastenausgleichsmodule und anderen Netzwerkgeräte verdecken häufig Informationen über die Anforderung, bevor diese die App erreicht:

* Wenn HTTPS-Anforderungen über HTTP-Proxy ausgeführt werden, geht das ursprüngliche Schema (HTTPS) verloren und muss in einem Header weitergeleitet werden.
* Da eine App eine Anforderung über den Proxy empfängt und nicht über ihre echte Quelle im Internet oder Unternehmensnetzwerk, muss die ursprüngliche Client-IP-Adresse ebenfalls in einem Header weitergeleitet werden.

Diese Informationen können im Anforderungsprozess, z.B. in Umleitungen, bei der Authentifizierung, der Linkgenerierung, der Richtlinienauswertung und der Client-Geolocation wichtig sein.

## <a name="forwarded-headers"></a>Weitergeleitete Header

Gemäß der Konvention leiten Proxys Informationen in HTTP-Headern weiter.

| Header | Beschreibung |
| ------ | ----------- |
| X-Forwarded-For | Enthält Informationen zum Client, der die Anforderung und die nachfolgenden Proxys in einer Kette von Proxys initiiert hat. Dieser Parameter kann IP-Adressen (und optional Portnummern) enthalten. Der erste Parameter in einer Kette von Proxyservern gibt den Client an, auf dem die Anforderung zuerst gesendet wurde. Darauf folgen weitere Proxybezeichner. Der letzte Proxy in der Kette ist nicht in der Liste der Parameter. Die IP-Adresse des letzten Proxys und optional eine Portnummer stehen als Remote IP-Adresse auf der Transportschicht zur Verfügung. |
| X-Forwarded-Proto | Der Wert des ursprünglichen Schemas (HTTP/HTTPS). Der Wert kann auch eine Liste von Schemas sein, wenn die Anforderung mehrere Proxys durchlaufen hat. |
| X-Forwarded-Host | Der ursprüngliche Wert des Felds „Hostheader“. In der Regel ändern Proxys den Hostheader nicht. Informationen zu einem Sicherheitsrisiko, das Rechteerweiterungen ermöglicht und sich auf Systeme auswirkt, in denen der Proxy Hostheader nicht validiert oder auf als unbedenklich bekannte Werte beschränkt, finden Sie in der [Microsoft-Sicherheitsempfehlung CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295). |

Die Middleware für weitergeleitete Header (<xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersMiddleware>) liest die Header und füllt die zugehörigen Felder in <xref:Microsoft.AspNetCore.Http.HttpContext> aus.

Die Middleware aktualisiert:

* [HttpContext.Connection.RemoteIpAddress:](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) Wird mit dem Headerwert `X-Forwarded-For` festgelegt. Zusätzliche Einstellungen beeinflussen, wie die Middleware `RemoteIpAddress` festlegt. Einzelheiten finden Sie unter [Middleware für weitergeleitete Header: Optionen](#forwarded-headers-middleware-options).
* [HttpContext.Request.Scheme:](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) Wird mit dem Headerwert `X-Forwarded-Proto` festgelegt.
* [HttpContext.Request.Host:](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) Wird mit dem Headerwert `X-Forwarded-Host` festgelegt.

Die [Standardeinstellungen](#forwarded-headers-middleware-options) der Middleware für weitergeleitete Header können konfiguriert werden. Folgende Standardeinstellungen sind verfügbar:

* Zwischen App und Quelle der Anforderungen ist nur ein *Proxy* vorhanden.
* Loopbackadressen sind für bekannte Proxys und bekannte Netzwerke konfiguriert.
* Die weitergeleiteten Header heißen `X-Forwarded-For` und `X-Forwarded-Proto`.

Beachten Sie, dass bei manchen Netzwerkgeräten eine zusätzliche Konfiguration erforderlich ist, damit die Header `X-Forwarded-For` und `X-Forwarded-Proto` hinzugefügt werden. Lesen Sie in der Anleitung des Geräteherstellers nach, wenn über einen Proxy übermittelte Anfragen ohne diese Header in der Anwendung eingehen. Verwendet das Gerät andere Headernamen als `X-Forwarded-For` und `X-Forwarded-Proto`, passen Sie die Optionen <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> und <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> entsprechend an. Weitere Informationen finden Sie in den Abschnitten [Middleware für weitergeleitete Header: Optionen](#forwarded-headers-middleware-options) und [Konfiguration für einen Proxy, der andere Headernamen nutzt](#configuration-for-a-proxy-that-uses-different-header-names).

## <a name="iisiis-express-and-aspnet-core-module"></a>IIS-/IIS Express und ASP.NET Core-Modul

Middleware für weitergeleitete Header wird standardmäßig durch [Middleware für die Integration von IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) aktiviert, wenn die App hinter IIS und dem ASP.NET Core-Modul von einem [Out-of-Process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)-Host gehostet wird. Middleware für weitergeleitete Header wird aktiviert und zuerst in der Middlewarepipeline mit einer beschränkten Konfiguration ausgeführt, die für das ASP.NET Core-Modul spezifisch ist. Dies ist auf Bedenken in Bezug auf die Vertrauenswürdigkeit von weitergeleiteten Headern zurückzuführen (z.B. [IP-Spoofing](https://www.iplocation.net/ip-spoofing)). Die Middleware wird so konfiguriert, dass sie die Header `X-Forwarded-For` und `X-Forwarded-Proto` weiterleitet, und ist auf einen einzelnen localhost-Proxy beschränkt. Wenn zusätzliche Konfigurationsschritte erforderlich sind, finden Sie weitere Informationen unter [Middleware für weitergeleitete Header: Optionen](#forwarded-headers-middleware-options).

## <a name="other-proxy-server-and-load-balancer-scenarios"></a>Andere Proxyserver- und Lastenausgleichsszenarios

Außer bei der Verwendung der [Integration von IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) wird die Middleware für weitergeleitete Header nicht standardmäßig aktiviert, wenn sie von einem [Out-of-Process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)-Host gehostet wird. Middleware für weitergeleitete Header muss aktiviert sein, damit eine App weitergeleitete Header mit <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> verarbeitet. Sind nach der Aktivierung der Middleware keine <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> für die Middleware angegeben, sind die standardmäßigen [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) gleich [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).

Konfigurieren Sie die Middleware mit <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> so, dass die Header `X-Forwarded-For` und `X-Forwarded-Proto` in `Startup.ConfigureServices` weitergeleitet werden.

<a name="fhmo"></a>

### <a name="forwarded-headers-middleware-order"></a>Middleware für weitergeleitete Header: Auftrag

Die Middleware für weitergeleitete Header muss noch vor einer anderen Middleware ausgeführt werden. Mit dieser Reihenfolge wird sichergestellt, dass die auf Informationen von weitergeleiteten Headern basierende Middleware die zu verarbeitenden Headerwerte nutzen kann. Die Middleware für weitergeleitete Header kann nach der Diagnose und Fehlerbehandlung ausgeführt werden. Sie muss jedoch vor dem Aufrufen von `UseHsts`ausgeführt werden:

[!code-csharp[](~/host-and-deploy/proxy-load-balancer/3.1samples/Startup.cs?name=snippet&highlight=13-17,25,30)]

Rufen Sie alternativ vor der Diagnose `UseForwardedHeaders` auf:

[!code-csharp[](~/host-and-deploy/proxy-load-balancer/3.1samples/Startup2.cs?name=snippet)]

> [!NOTE]
> Sind keine <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> in `Startup.ConfigureServices` oder direkt für die Erweiterungsmethode mit <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> angegeben, sind die weiterzuleitenden Standardheader gleich [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders). Die <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders>-Eigenschaft muss mit den weiterzuleitenden Headern konfiguriert werden.

## <a name="nginx-configuration"></a>Nginx-Konfiguration

Informationen zur Weiterleitung der Header `X-Forwarded-For` und `X-Forwarded-Proto` finden Sie unter <xref:host-and-deploy/linux-nginx#configure-nginx>. Weitere Informationen finden Sie unter [NGINX: Using the Forwarded header (NGINX: Verwenden des weitergeleiteten Headers)](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).

## <a name="apache-configuration"></a>Apache-Konfiguration

`X-Forwarded-For` wird automatisch hinzugefügt (siehe [Apache Module mod_proxy: Reverse Proxy Request Headers (Apache-Modul mod_proxy: Anforderungsheader für Reverseproxys)](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)). Informationen zur Weiterleitung des Headers `X-Forwarded-Proto` finden Sie unter <xref:host-and-deploy/linux-apache#configure-apache>.

## <a name="forwarded-headers-middleware-options"></a>Middleware für weitergeleitete Header: Optionen

<xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> steuern das Verhalten der Middleware für weitergeleitete Header. Im folgenden Beispiel werden die Standardwerte geändert:

* Beschränken Sie die Zahl der Einträge in den weitergeleiteten Headern auf `2`.
* Fügen Sie eine bekannte Proxyadresse von `127.0.10.1` hinzu.
* Ändern Sie den Namen des weitergeleiteten Headers vom Standardwert `X-Forwarded-For` in den Wert `X-Forwarded-For-My-Custom-Header-Name`.

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| Option | Beschreibung |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Begrenzt Hosts durch den `X-Forwarded-Host`-Header auf die angegebenen Werte.<ul><li>Werte werden mit Ordnungszahl/Groß-/Kleinschreibung ignorieren verglichen.</li><li>Portnummern müssen ausgeschlossen werden.</li><li>Wenn die Liste leer ist, sind alle Hosts zulässig.</li><li>Ein Platzhalter `*` auf der obersten Ebene lässt alle nicht leeren Hosts zu.</li><li>Unterdomänen-Platzhalter sind zulässig, stimmen aber nicht mit der Stammdomäne überein. Beispielsweise entspricht `*.contoso.com` der Unterdomäne `foo.contoso.com`, aber nicht der Stammdomäne `contoso.com`.</li><li>Unicode-Hostnamen sind zulässig, werden jedoch für den Abgleich in [Punycode](https://tools.ietf.org/html/rfc3492) konvertiert.</li><li>[IPv6-Adressen](https://tools.ietf.org/html/rfc4291) müssen Begrenzungsklammern einschließen und im [konventionellen Format](https://tools.ietf.org/html/rfc4291#section-2.2) vorliegen (z.B. `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`). IPv6-Adressen sind keine Sonderfälle, um auf logische Gleichheit zwischen verschiedenen Formaten zu prüfen, und es wird keine Kanonisierung durchgeführt.</li><li>Dadurch, dass die zulässigen Hosts nicht begrenzt werden, kann einem Angreifer die Möglichkeit eröffnet werden, von dem Dienst generierte Links zu fälschen.</li></ul>Der Standardwert ist eine leere `IList<string>`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName) angegebenen. Diese Option wird verwendet, wenn beim Proxy/Weiterleitenden nicht der Header `X-Forwarded-For` sondern ein anderer Header für die Weiterleitung der Informationen genutzt wird.<br><br>Der Standardwert ist `X-Forwarded-For`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Gibt an, welche Weiterleitungen verarbeitet werden sollen. Weitere Informationen zur Liste der anzuwendenden Felder finden Sie unter [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders). Typische Werte, die dieser Eigenschaft zugewiesen wurden, sind `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.<br><br>Der Standardwert ist [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders). |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName) angegebenen. Diese Option wird verwendet, wenn beim Proxy/Weiterleitenden nicht der Header `X-Forwarded-Host` sondern ein anderer Header für die Weiterleitung der Informationen genutzt wird.<br><br>Der Standardwert ist `X-Forwarded-Host`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName) angegebenen. Diese Option wird verwendet, wenn beim Proxy/Weiterleitenden nicht der Header `X-Forwarded-Proto` sondern ein anderer Header für die Weiterleitung der Informationen genutzt wird.<br><br>Der Standardwert ist `X-Forwarded-Proto`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | Schränkt die Anzahl der Einträge in den Headern ein, die verarbeitet werden. Legen Sie den Wert `null` fest, um die Einschränkung zu deaktivieren. Diese Einstellung sollte jedoch nur vorgenommen werden, wenn `KnownProxies` oder `KnownNetworks` konfiguriert sind. Das Festlegen eines Werts ungleich `null` ist eine Vorsichtsmaßnahme (aber keine Garantie) zum Schutz vor falsch konfigurierten Proxys und schädlichen Anforderungen aus Seitenkanälen im Netzwerk.<br><br>Die Middleware der Weiterleitungsheader verarbeitet Header in umgekehrter Reihenfolge von rechts nach links. Wenn der Standardwert von (`1`) verwendet wird, wird nur der äußere rechte Wert aus den Headern verarbeitet, es sei denn, der Wert von `ForwardLimit` wird erhöht.<br><br>Der Standardwert ist `1`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | Adressbereiche bekannter Netzwerke; von dort weitergeleitete Header müssen akzeptiert werden. Geben Sie IP-Adressbereiche mithilfe der CIDR-Notation (Classless Interdomain Routing) an.<br><br>Wenn der Server Dualmodussockets verwendet, werden IPv4-Adressen in einem IPv6-Format bereitgestellt (z.B. wird `10.0.0.1` in IPv4 in IPv6 als `::ffff:10.0.0.1` dargestellt). Siehe [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). Bestimmen Sie mithilfe der [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*), ob dieses Format erforderlich ist. Weitere Informationen finden Sie im Abschnitt [Konfiguration für eine IPv4-Adresse, die als IPv6-Adresse dargestellt wird](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).<br><br>Der Standardwert ist `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> mit einem Eintrag für `IPAddress.Loopback`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | Adressen bekannter Proxys, von denen weitergeleitete Header akzeptiert werden müssen. Verwenden Sie `KnownProxies`, um genaue IP-Adressübereinstimmungen anzugeben.<br><br>Wenn der Server Dualmodussockets verwendet, werden IPv4-Adressen in einem IPv6-Format bereitgestellt (z.B. wird `10.0.0.1` in IPv4 in IPv6 als `::ffff:10.0.0.1` dargestellt). Siehe [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). Bestimmen Sie mithilfe der [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*), ob dieses Format erforderlich ist. Weitere Informationen finden Sie im Abschnitt [Konfiguration für eine IPv4-Adresse, die als IPv6-Adresse dargestellt wird](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).<br><br>Der Standardwert ist `IList`\<<xref:System.Net.IPAddress>> mit einem Eintrag für `IPAddress.IPv6Loopback`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName) angegebenen.<br><br>Der Standardwert ist `X-Original-For`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName) angegebenen.<br><br>Der Standardwert ist `X-Original-Host`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName) angegebenen.<br><br>Der Standardwert ist `X-Original-Proto`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Die Anzahl von Headerwerten muss in den [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) übereinstimmen, die verarbeitet werden.<br><br>Die Standardeinstellung in ASP.NET Core 1.x ist `true`. Die Standardeinstellung in ASP.NET Core 2.0 oder höher ist `false`. |

## <a name="scenarios-and-use-cases"></a>Szenarios und Anwendungsfälle

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a>Wenn keine weitergeleiteten Header hinzugefügt werden können und alle Anforderungen sicher sind

In einigen Fällen ist es möglicherweise ausgeschlossen, weitergeleitete Header den Anforderungen hinzuzufügen, die über einen Proxy an die App übermittelt werden. Wenn der Proxy erzwingt, dass alle öffentlichen externen Anforderungen HTTPS-Anforderungen sind, kann das Schema manuell in `Startup.Configure` festgelegt werden, bevor ein beliebiger Middlewaretyp verwendet wird:

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

Dieser Code kann mit einer Umgebungsvariablen oder einer anderen Konfigurationseinstellung in einer Entwicklungs- oder Stagingumgebung deaktiviert werden.

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a>Umgang mit Pfadbasis und Proxys, die den Anforderungspfad ändern

Einige Proxys übergeben den Pfad intakt, aber mit einem App-basierten Pfad, der entfernt werden sollte, damit das Routing ordnungsgemäß funktioniert. [UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*)-Middleware teilt den Pfad in [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) und den App-Basispfad in [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).

Wenn `/foo` der App-Basispfad für einen als `/foo/api/1` übergebenen Proxypfad ist, setzt die Middleware mit dem folgenden Befehl `Request.PathBase` auf `/foo` und `Request.Path` auf `/api/1`:

```csharp
app.UsePathBase("/foo");
```

Der ursprüngliche Pfad und die Pfadbasis werden erneut angewendet, wenn die Middleware in umgekehrter Reihenfolge erneut aufgerufen wird. Weitere Informationen zur Verarbeitungsreihenfolge in der Middleware finden Sie unter <xref:fundamentals/middleware/index>.

Wenn der Proxy den Pfad abschneidet (z.B. Weiterleitung von `/foo/api/1` zu `/api/1`), korrigieren Sie Umleitungen und Links, indem Sie die Eigenschaft [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) der Anforderungen setzen:

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

Wenn der Proxy Pfaddaten hinzufügt, verwerfen Sie einen Teil des Pfads, um Umleitungen und Links zu korrigieren, indem Sie <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> verwenden und der <xref:Microsoft.AspNetCore.Http.HttpRequest.Path>-Eigenschaft zuweisen:

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a>Konfiguration für einen Proxy, der andere Headernamen verwendet

Verwendet der Proxy zum Weiterleiten der Proxyadresse/des Ports und zum Erzeugen der Schemainformationen andere Headernamen als `X-Forwarded-For` und `X-Forwarded-Proto`, passen Sie die Optionen <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> und <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> entsprechend an:

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a>Konfiguration für eine IPv4-Adresse, die als IPv6-Adresse dargestellt wird

Wenn der Server Dualmodussockets verwendet, werden IPv4-Adressen in einem IPv6-Format bereitgestellt (z.B. wird `10.0.0.1` in IPv4 in IPv6 als `::ffff:10.0.0.1` oder `::ffff:a00:1` dargestellt). Siehe [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). Bestimmen Sie mithilfe der [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*), ob dieses Format erforderlich ist.

Im folgenden Beispiel wird eine Netzwerkadresse, die weitergeleitete Header bereitstellt, der `KnownNetworks`-Liste im IPv6-Format hinzugefügt.

IPv4-Adresse: `10.11.12.1/8`

Konvertierte IPv6-Adresse: `::ffff:10.11.12.1`  
Konvertierte Präfixlänge: 104

Sie können die Adresse auch im Hexadezimalformat angeben (`10.11.12.1` in IPv6 als `::ffff:0a0b:0c01` dargestellt). Fügen Sie bei der Konvertierung einer IPv4-Adresse in IPv6 der CIDR-Präfixlänge 96 (`8` im Beispiel) für das zusätzliche `::ffff:`-IPv6-Präfix hinzu (8 + 96 = 104). 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a>Das Schema für Linux- und Nicht-IIS-Reverseproxys weiterleiten

Apps, die <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> und <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> aufrufen, versetzen eine Site in eine Endlosschleife, falls die Bereitstellung in einem Azure Linux App Service, auf einem virtuellen Azure Linux-Computer, oder hinter einem anderen Reverseproxy als IIS erfolgt. TLS wird vom Reverseproxy beendet, und Kestrel wird nicht auf das richtige Anforderungsschema aufmerksam gemacht. OAuth und OIDC schlagen in dieser Konfiguration ebenfalls fehl, weil sie falsche Umleitungen generieren. <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> fügt die Middleware für weitergeleitete Header hinzu und konfiguriert sie, wenn es hinter IIS ausgeführt wird, aber es gibt keine entsprechende automatische Konfiguration für Linux (Apache- oder Nginx-Integration).

Um das Schema von dem Proxy in Nicht-IIS-Szenarios weiterzuleiten, fügen Sie die Middleware für weitergeleitete Header hinzu, und konfigurieren Sie sie. In `Startup.ConfigureServices` verwenden Sie folgenden Code:

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="certificate-forwarding"></a>Zertifikatweiterleitung 

### <a name="azure"></a>Azure

Informationen zum Konfigurieren von Azure App Service für die Zertifikatsweiterleitung finden Sie unter [Konfigurieren der gegenseitigen TLS-Authentifizierung für Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth). Die folgende Anleitung bezieht sich auf die Konfiguration der ASP.NET Core-App.

Fügen Sie `Startup.Configure` direkt vor dem Aufruf von `app.UseAuthentication();` den folgenden Code hinzu:

```csharp
app.UseCertificateForwarding();
```


Konfigurieren Sie die Middleware für die Zertifikatweiterleitung, um den von Azure verwendeten Headernamen anzugeben. Fügen Sie in `Startup.ConfigureServices` den folgenden Code hinzu, um den Header zu konfigurieren, anhand dessen die Middleware ein Zertifikat erstellt:

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a>Andere Webproxys

Wenn ein Proxy verwendet wird, der nicht IIS oder Routing von Anwendungsanforderungen von Azure App Service entspricht, konfigurieren Sie den Proxy so, dass das empfangene Zertifikat in einem HTTP-Header weitergeleitet wird. Fügen Sie `Startup.Configure` direkt vor dem Aufruf von `app.UseAuthentication();` den folgenden Code hinzu:

```csharp
app.UseCertificateForwarding();
```

Konfigurieren Sie die Middleware für die Zertifikatweiterleitung konfigurieren, um den Headernamen anzugeben. Fügen Sie in `Startup.ConfigureServices` den folgenden Code hinzu, um den Header zu konfigurieren, anhand dessen die Middleware ein Zertifikat erstellt:

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

Wenn der Proxy das Zertifikat mit nicht mit base64 codiert (wie bei Nginx), legen Sie die Option `HeaderConverter` fest. Betrachten Sie das folgende Beispiel in `Startup.ConfigureServices`:

```csharp
services.AddCertificateForwarding(options =>
{
    options.CertificateHeader = "YOUR_CUSTOM_HEADER_NAME";
    options.HeaderConverter = (headerValue) => 
    {
        var clientCertificate = 
           /* some conversion logic to create an X509Certificate2 */
        return clientCertificate;
    }
});
```

## <a name="troubleshoot"></a>Problembehandlung

Wenn Header nicht wie erwartet zugewiesen werden, aktivieren Sie die [Protokollierung](xref:fundamentals/logging/index). Wenn die Protokolle nicht genügend Informationen zur Problembehandlung bereitstellen, listen Sie die vom Server empfangenen Anforderungsheader auf. Verwenden Sie Inlinemiddleware, um Anforderungsheader in eine App-Antwort zu schreiben oder die Header zu protokollieren. 

Um die Header in die Antwort der App zu schreiben, platzieren Sie die folgende Terminalinlinemiddleware unmittelbar hinter den Aufruf von <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

Statt in den Antworttext können Sie in Protokolle schreiben. Das Schreiben in Protokolle ermöglicht es der Website, während des Debuggens normal zu funktionieren.

So schreiben Sie in Protokolle und nicht in den Antworttext:

* Fügen Sie `ILogger<Startup>` in die `Startup`-Klasse ein, wie unter [Erstellen von Protokollen beim Start](xref:fundamentals/logging/index#create-logs-in-startup) beschrieben.
* Platzieren Sie die folgende Inlinemiddleware unmittelbar hinter den Aufruf von <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

Bei der Verarbeitung werden `X-Forwarded-{For|Proto|Host}`-Werte in `X-Original-{For|Proto|Host}` verschoben. Wenn in einem bestimmten Header mehrere Werte vorhanden sind, verarbeitet die Middleware der Weiterleitungsheader die Header in umgekehrter Reihenfolge von rechts nach links. Der Standardwert von `ForwardLimit` ist `1` (eins), sodass nur der äußere rechte Wert aus den Headern verarbeitet wird, es sei denn, der Wert von `ForwardLimit` wird erhöht.

Die ursprüngliche IP-Remoteadresse der Anforderung muss mit einem Eintrag in den `KnownProxies`- oder `KnownNetworks`-Listen übereinstimmen, bevor weitergeleitete Header verarbeitet werden. Auf diese Weise wird das Headerspoofing begrenzt, da keine Weiterleitungen von nicht vertrauenswürdigen Proxys akzeptiert werden. Wenn ein unbekannter Proxy erkannt wird, gibt die Protokollierung die Adresse des Proxys an:

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

Im vorherigen Beispiel ist 10.0.0.100 ein Proxyserver. Wenn der Server ein vertrauenswürdiger Proxy ist, fügen Sie die IP-Adresse des Servers `KnownProxies` (oder ein vertrauenswürdiges Netzwerk `KnownNetworks`) in `Startup.ConfigureServices` hinzu. Weitere Informationen finden Sie im Abschnitt [Middleware für weitergeleitete Header: Optionen](#forwarded-headers-middleware-options).

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> Erlauben Sie nur vertrauenswürdigen Proxys und Netzwerken die Weiterleitung von Headern. Andernfalls sind Angriffe des Typs [IP-Spoofing](https://www.iplocation.net/ip-spoofing) möglich.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:host-and-deploy/web-farm>
* [Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability (Microsoft-Sicherheitsempfehlung CVE-2018-0787: Sicherheitsrisiko durch Rechteerweiterungen in ASP.NET Core)](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

In der für ASP.NET Core empfohlenen Konfiguration wird die App mit dem IIS/ASP.NET Core-Modul, Nginx oder Apache gehostet. Proxyserver, Lastenausgleichsmodule und anderen Netzwerkgeräte verdecken häufig Informationen über die Anforderung, bevor diese die App erreicht:

* Wenn HTTPS-Anforderungen über HTTP-Proxy ausgeführt werden, geht das ursprüngliche Schema (HTTPS) verloren und muss in einem Header weitergeleitet werden.
* Da eine App eine Anforderung über den Proxy empfängt und nicht über ihre echte Quelle im Internet oder Unternehmensnetzwerk, muss die ursprüngliche Client-IP-Adresse ebenfalls in einem Header weitergeleitet werden.

Diese Informationen können im Anforderungsprozess, z.B. in Umleitungen, bei der Authentifizierung, der Linkgenerierung, der Richtlinienauswertung und der Client-Geolocation wichtig sein.

## <a name="forwarded-headers"></a>Weitergeleitete Header

Gemäß der Konvention leiten Proxys Informationen in HTTP-Headern weiter.

| Header | Beschreibung |
| ------ | ----------- |
| X-Forwarded-For | Enthält Informationen zum Client, der die Anforderung und die nachfolgenden Proxys in einer Kette von Proxys initiiert hat. Dieser Parameter kann IP-Adressen (und optional Portnummern) enthalten. Der erste Parameter in einer Kette von Proxyservern gibt den Client an, auf dem die Anforderung zuerst gesendet wurde. Darauf folgen weitere Proxybezeichner. Der letzte Proxy in der Kette ist nicht in der Liste der Parameter. Die IP-Adresse des letzten Proxys und optional eine Portnummer stehen als Remote IP-Adresse auf der Transportschicht zur Verfügung. |
| X-Forwarded-Proto | Der Wert des ursprünglichen Schemas (HTTP/HTTPS). Der Wert kann auch eine Liste von Schemas sein, wenn die Anforderung mehrere Proxys durchlaufen hat. |
| X-Forwarded-Host | Der ursprüngliche Wert des Felds „Hostheader“. In der Regel ändern Proxys den Hostheader nicht. Informationen zu einem Sicherheitsrisiko, das Rechteerweiterungen ermöglicht und sich auf Systeme auswirkt, in denen der Proxy Hostheader nicht validiert oder auf als unbedenklich bekannte Werte beschränkt, finden Sie in der [Microsoft-Sicherheitsempfehlung CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295). |

Die Middleware für weitergeleitete Header aus dem Paket [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) liest die Header und füllt die zugehörigen Felder in <xref:Microsoft.AspNetCore.Http.HttpContext> aus.

Die Middleware aktualisiert:

* [HttpContext.Connection.RemoteIpAddress:](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) Wird mit dem Headerwert `X-Forwarded-For` festgelegt. Zusätzliche Einstellungen beeinflussen, wie die Middleware `RemoteIpAddress` festlegt. Einzelheiten finden Sie unter [Middleware für weitergeleitete Header: Optionen](#forwarded-headers-middleware-options).
* [HttpContext.Request.Scheme:](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) Wird mit dem Headerwert `X-Forwarded-Proto` festgelegt.
* [HttpContext.Request.Host:](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) Wird mit dem Headerwert `X-Forwarded-Host` festgelegt.

Die [Standardeinstellungen](#forwarded-headers-middleware-options) der Middleware für weitergeleitete Header können konfiguriert werden. Folgende Standardeinstellungen sind verfügbar:

* Zwischen App und Quelle der Anforderungen ist nur ein *Proxy* vorhanden.
* Loopbackadressen sind für bekannte Proxys und bekannte Netzwerke konfiguriert.
* Die weitergeleiteten Header heißen `X-Forwarded-For` und `X-Forwarded-Proto`.

Beachten Sie, dass bei manchen Netzwerkgeräten eine zusätzliche Konfiguration erforderlich ist, damit die Header `X-Forwarded-For` und `X-Forwarded-Proto` hinzugefügt werden. Lesen Sie in der Anleitung des Geräteherstellers nach, wenn über einen Proxy übermittelte Anfragen ohne diese Header in der Anwendung eingehen. Verwendet das Gerät andere Headernamen als `X-Forwarded-For` und `X-Forwarded-Proto`, passen Sie die Optionen <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> und <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> entsprechend an. Weitere Informationen finden Sie in den Abschnitten [Middleware für weitergeleitete Header: Optionen](#forwarded-headers-middleware-options) und [Konfiguration für einen Proxy, der andere Headernamen nutzt](#configuration-for-a-proxy-that-uses-different-header-names).

## <a name="iisiis-express-and-aspnet-core-module"></a>IIS-/IIS Express und ASP.NET Core-Modul

Middleware für weitergeleitete Header wird standardmäßig durch [Middleware für die Integration von IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) aktiviert, wenn die App hinter IIS und dem ASP.NET Core-Modul von einem [Out-of-Process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)-Host gehostet wird. Middleware für weitergeleitete Header wird aktiviert und zuerst in der Middlewarepipeline mit einer beschränkten Konfiguration ausgeführt, die für das ASP.NET Core-Modul spezifisch ist. Dies ist auf Bedenken in Bezug auf die Vertrauenswürdigkeit von weitergeleiteten Headern zurückzuführen (z.B. [IP-Spoofing](https://www.iplocation.net/ip-spoofing)). Die Middleware wird so konfiguriert, dass sie die Header `X-Forwarded-For` und `X-Forwarded-Proto` weiterleitet, und ist auf einen einzelnen localhost-Proxy beschränkt. Wenn zusätzliche Konfigurationsschritte erforderlich sind, finden Sie weitere Informationen unter [Middleware für weitergeleitete Header: Optionen](#forwarded-headers-middleware-options).

## <a name="other-proxy-server-and-load-balancer-scenarios"></a>Andere Proxyserver- und Lastenausgleichsszenarios

Außer bei der Verwendung der [Integration von IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) wird die Middleware für weitergeleitete Header nicht standardmäßig aktiviert, wenn sie von einem [Out-of-Process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)-Host gehostet wird. Middleware für weitergeleitete Header muss aktiviert sein, damit eine App weitergeleitete Header mit <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> verarbeitet. Sind nach der Aktivierung der Middleware keine <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> für die Middleware angegeben, sind die standardmäßigen [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) gleich [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).

Konfigurieren Sie die Middleware mit <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> so, dass die Header `X-Forwarded-For` und `X-Forwarded-Proto` in `Startup.ConfigureServices` weitergeleitet werden. Rufen Sie die Methode <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure` auf, bevor Sie andere Middleware aufrufen:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = 
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
    }

    app.UseStaticFiles();
    // In ASP.NET Core 1.x, replace the following line with: app.UseIdentity();
    app.UseAuthentication();
    app.UseMvc();
}
```

> [!NOTE]
> Sind keine <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> in `Startup.ConfigureServices` oder direkt für die Erweiterungsmethode mit <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> angegeben, sind die weiterzuleitenden Standardheader gleich [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders). Die <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders>-Eigenschaft muss mit den weiterzuleitenden Headern konfiguriert werden.

## <a name="nginx-configuration"></a>Nginx-Konfiguration

Informationen zur Weiterleitung der Header `X-Forwarded-For` und `X-Forwarded-Proto` finden Sie unter <xref:host-and-deploy/linux-nginx#configure-nginx>. Weitere Informationen finden Sie unter [NGINX: Using the Forwarded header (NGINX: Verwenden des weitergeleiteten Headers)](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).

## <a name="apache-configuration"></a>Apache-Konfiguration

`X-Forwarded-For` wird automatisch hinzugefügt (siehe [Apache Module mod_proxy: Reverse Proxy Request Headers (Apache-Modul mod_proxy: Anforderungsheader für Reverseproxys)](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)). Informationen zur Weiterleitung des Headers `X-Forwarded-Proto` finden Sie unter <xref:host-and-deploy/linux-apache#configure-apache>.

## <a name="forwarded-headers-middleware-options"></a>Middleware für weitergeleitete Header: Optionen

<xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> steuern das Verhalten der Middleware für weitergeleitete Header. Im folgenden Beispiel werden die Standardwerte geändert:

* Beschränken Sie die Zahl der Einträge in den weitergeleiteten Headern auf `2`.
* Fügen Sie eine bekannte Proxyadresse von `127.0.10.1` hinzu.
* Ändern Sie den Namen des weitergeleiteten Headers vom Standardwert `X-Forwarded-For` in den Wert `X-Forwarded-For-My-Custom-Header-Name`.

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| Option | Beschreibung |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Begrenzt Hosts durch den `X-Forwarded-Host`-Header auf die angegebenen Werte.<ul><li>Werte werden mit Ordnungszahl/Groß-/Kleinschreibung ignorieren verglichen.</li><li>Portnummern müssen ausgeschlossen werden.</li><li>Wenn die Liste leer ist, sind alle Hosts zulässig.</li><li>Ein Platzhalter `*` auf der obersten Ebene lässt alle nicht leeren Hosts zu.</li><li>Unterdomänen-Platzhalter sind zulässig, stimmen aber nicht mit der Stammdomäne überein. Beispielsweise entspricht `*.contoso.com` der Unterdomäne `foo.contoso.com`, aber nicht der Stammdomäne `contoso.com`.</li><li>Unicode-Hostnamen sind zulässig, werden jedoch für den Abgleich in [Punycode](https://tools.ietf.org/html/rfc3492) konvertiert.</li><li>[IPv6-Adressen](https://tools.ietf.org/html/rfc4291) müssen Begrenzungsklammern einschließen und im [konventionellen Format](https://tools.ietf.org/html/rfc4291#section-2.2) vorliegen (z.B. `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`). IPv6-Adressen sind keine Sonderfälle, um auf logische Gleichheit zwischen verschiedenen Formaten zu prüfen, und es wird keine Kanonisierung durchgeführt.</li><li>Dadurch, dass die zulässigen Hosts nicht begrenzt werden, kann einem Angreifer die Möglichkeit eröffnet werden, von dem Dienst generierte Links zu fälschen.</li></ul>Der Standardwert ist eine leere `IList<string>`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName) angegebenen. Diese Option wird verwendet, wenn beim Proxy/Weiterleitenden nicht der Header `X-Forwarded-For` sondern ein anderer Header für die Weiterleitung der Informationen genutzt wird.<br><br>Der Standardwert ist `X-Forwarded-For`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Gibt an, welche Weiterleitungen verarbeitet werden sollen. Weitere Informationen zur Liste der anzuwendenden Felder finden Sie unter [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders). Typische Werte, die dieser Eigenschaft zugewiesen wurden, sind `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.<br><br>Der Standardwert ist [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders). |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName) angegebenen. Diese Option wird verwendet, wenn beim Proxy/Weiterleitenden nicht der Header `X-Forwarded-Host` sondern ein anderer Header für die Weiterleitung der Informationen genutzt wird.<br><br>Der Standardwert ist `X-Forwarded-Host`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName) angegebenen. Diese Option wird verwendet, wenn beim Proxy/Weiterleitenden nicht der Header `X-Forwarded-Proto` sondern ein anderer Header für die Weiterleitung der Informationen genutzt wird.<br><br>Der Standardwert ist `X-Forwarded-Proto`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | Schränkt die Anzahl der Einträge in den Headern ein, die verarbeitet werden. Legen Sie den Wert `null` fest, um die Einschränkung zu deaktivieren. Diese Einstellung sollte jedoch nur vorgenommen werden, wenn `KnownProxies` oder `KnownNetworks` konfiguriert sind. Das Festlegen eines Werts ungleich `null` ist eine Vorsichtsmaßnahme (aber keine Garantie) zum Schutz vor falsch konfigurierten Proxys und schädlichen Anforderungen aus Seitenkanälen im Netzwerk.<br><br>Die Middleware der Weiterleitungsheader verarbeitet Header in umgekehrter Reihenfolge von rechts nach links. Wenn der Standardwert von (`1`) verwendet wird, wird nur der äußere rechte Wert aus den Headern verarbeitet, es sei denn, der Wert von `ForwardLimit` wird erhöht.<br><br>Der Standardwert ist `1`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | Adressbereiche bekannter Netzwerke; von dort weitergeleitete Header müssen akzeptiert werden. Geben Sie IP-Adressbereiche mithilfe der CIDR-Notation (Classless Interdomain Routing) an.<br><br>Wenn der Server Dualmodussockets verwendet, werden IPv4-Adressen in einem IPv6-Format bereitgestellt (z.B. wird `10.0.0.1` in IPv4 in IPv6 als `::ffff:10.0.0.1` dargestellt). Siehe [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). Bestimmen Sie mithilfe der [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*), ob dieses Format erforderlich ist. Weitere Informationen finden Sie im Abschnitt [Konfiguration für eine IPv4-Adresse, die als IPv6-Adresse dargestellt wird](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).<br><br>Der Standardwert ist `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> mit einem Eintrag für `IPAddress.Loopback`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | Adressen bekannter Proxys, von denen weitergeleitete Header akzeptiert werden müssen. Verwenden Sie `KnownProxies`, um genaue IP-Adressübereinstimmungen anzugeben.<br><br>Wenn der Server Dualmodussockets verwendet, werden IPv4-Adressen in einem IPv6-Format bereitgestellt (z.B. wird `10.0.0.1` in IPv4 in IPv6 als `::ffff:10.0.0.1` dargestellt). Siehe [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). Bestimmen Sie mithilfe der [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*), ob dieses Format erforderlich ist. Weitere Informationen finden Sie im Abschnitt [Konfiguration für eine IPv4-Adresse, die als IPv6-Adresse dargestellt wird](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).<br><br>Der Standardwert ist `IList`\<<xref:System.Net.IPAddress>> mit einem Eintrag für `IPAddress.IPv6Loopback`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName) angegebenen.<br><br>Der Standardwert ist `X-Original-For`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName) angegebenen.<br><br>Der Standardwert ist `X-Original-Host`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName) angegebenen.<br><br>Der Standardwert ist `X-Original-Proto`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Die Anzahl von Headerwerten muss in den [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) übereinstimmen, die verarbeitet werden.<br><br>Die Standardeinstellung in ASP.NET Core 1.x ist `true`. Die Standardeinstellung in ASP.NET Core 2.0 oder höher ist `false`. |

## <a name="scenarios-and-use-cases"></a>Szenarios und Anwendungsfälle

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a>Wenn keine weitergeleiteten Header hinzugefügt werden können und alle Anforderungen sicher sind

In einigen Fällen ist es möglicherweise ausgeschlossen, weitergeleitete Header den Anforderungen hinzuzufügen, die über einen Proxy an die App übermittelt werden. Wenn der Proxy erzwingt, dass alle öffentlichen externen Anforderungen HTTPS-Anforderungen sind, kann das Schema manuell in `Startup.Configure` festgelegt werden, bevor ein beliebiger Middlewaretyp verwendet wird:

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

Dieser Code kann mit einer Umgebungsvariablen oder einer anderen Konfigurationseinstellung in einer Entwicklungs- oder Stagingumgebung deaktiviert werden.

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a>Umgang mit Pfadbasis und Proxys, die den Anforderungspfad ändern

Einige Proxys übergeben den Pfad intakt, aber mit einem App-basierten Pfad, der entfernt werden sollte, damit das Routing ordnungsgemäß funktioniert. [UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*)-Middleware teilt den Pfad in [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) und den App-Basispfad in [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).

Wenn `/foo` der App-Basispfad für einen als `/foo/api/1` übergebenen Proxypfad ist, setzt die Middleware mit dem folgenden Befehl `Request.PathBase` auf `/foo` und `Request.Path` auf `/api/1`:

```csharp
app.UsePathBase("/foo");
```

Der ursprüngliche Pfad und die Pfadbasis werden erneut angewendet, wenn die Middleware in umgekehrter Reihenfolge erneut aufgerufen wird. Weitere Informationen zur Verarbeitungsreihenfolge in der Middleware finden Sie unter <xref:fundamentals/middleware/index>.

Wenn der Proxy den Pfad abschneidet (z.B. Weiterleitung von `/foo/api/1` zu `/api/1`), korrigieren Sie Umleitungen und Links, indem Sie die Eigenschaft [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) der Anforderungen setzen:

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

Wenn der Proxy Pfaddaten hinzufügt, verwerfen Sie einen Teil des Pfads, um Umleitungen und Links zu korrigieren, indem Sie <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> verwenden und der <xref:Microsoft.AspNetCore.Http.HttpRequest.Path>-Eigenschaft zuweisen:

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a>Konfiguration für einen Proxy, der andere Headernamen verwendet

Verwendet der Proxy zum Weiterleiten der Proxyadresse/des Ports und zum Erzeugen der Schemainformationen andere Headernamen als `X-Forwarded-For` und `X-Forwarded-Proto`, passen Sie die Optionen <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> und <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> entsprechend an:

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a>Konfiguration für eine IPv4-Adresse, die als IPv6-Adresse dargestellt wird

Wenn der Server Dualmodussockets verwendet, werden IPv4-Adressen in einem IPv6-Format bereitgestellt (z.B. wird `10.0.0.1` in IPv4 in IPv6 als `::ffff:10.0.0.1` oder `::ffff:a00:1` dargestellt). Siehe [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). Bestimmen Sie mithilfe der [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*), ob dieses Format erforderlich ist.

Im folgenden Beispiel wird eine Netzwerkadresse, die weitergeleitete Header bereitstellt, der `KnownNetworks`-Liste im IPv6-Format hinzugefügt.

IPv4-Adresse: `10.11.12.1/8`

Konvertierte IPv6-Adresse: `::ffff:10.11.12.1`  
Konvertierte Präfixlänge: 104

Sie können die Adresse auch im Hexadezimalformat angeben (`10.11.12.1` in IPv6 als `::ffff:0a0b:0c01` dargestellt). Fügen Sie bei der Konvertierung einer IPv4-Adresse in IPv6 der CIDR-Präfixlänge 96 (`8` im Beispiel) für das zusätzliche `::ffff:`-IPv6-Präfix hinzu (8 + 96 = 104). 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a>Das Schema für Linux- und Nicht-IIS-Reverseproxys weiterleiten

Apps, die <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> und <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> aufrufen, versetzen eine Site in eine Endlosschleife, falls die Bereitstellung in einem Azure Linux App Service, auf einem virtuellen Azure Linux-Computer, oder hinter einem anderen Reverseproxy als IIS erfolgt. TLS wird vom Reverseproxy beendet, und Kestrel wird nicht auf das richtige Anforderungsschema aufmerksam gemacht. OAuth und OIDC schlagen in dieser Konfiguration ebenfalls fehl, weil sie falsche Umleitungen generieren. <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> fügt die Middleware für weitergeleitete Header hinzu und konfiguriert sie, wenn es hinter IIS ausgeführt wird, aber es gibt keine entsprechende automatische Konfiguration für Linux (Apache- oder Nginx-Integration).

Um das Schema von dem Proxy in Nicht-IIS-Szenarios weiterzuleiten, fügen Sie die Middleware für weitergeleitete Header hinzu, und konfigurieren Sie sie. In `Startup.ConfigureServices` verwenden Sie folgenden Code:

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="troubleshoot"></a>Problembehandlung

Wenn Header nicht wie erwartet zugewiesen werden, aktivieren Sie die [Protokollierung](xref:fundamentals/logging/index). Wenn die Protokolle nicht genügend Informationen zur Problembehandlung bereitstellen, listen Sie die vom Server empfangenen Anforderungsheader auf. Verwenden Sie Inlinemiddleware, um Anforderungsheader in eine App-Antwort zu schreiben oder die Header zu protokollieren. 

Um die Header in die Antwort der App zu schreiben, platzieren Sie die folgende Terminalinlinemiddleware unmittelbar hinter den Aufruf von <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

Statt in den Antworttext können Sie in Protokolle schreiben. Das Schreiben in Protokolle ermöglicht es der Website, während des Debuggens normal zu funktionieren.

So schreiben Sie in Protokolle und nicht in den Antworttext:

* Fügen Sie `ILogger<Startup>` in die `Startup`-Klasse ein, wie unter [Erstellen von Protokollen beim Start](xref:fundamentals/logging/index#create-logs-in-startup) beschrieben.
* Platzieren Sie die folgende Inlinemiddleware unmittelbar hinter den Aufruf von <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

Bei der Verarbeitung werden `X-Forwarded-{For|Proto|Host}`-Werte in `X-Original-{For|Proto|Host}` verschoben. Wenn in einem bestimmten Header mehrere Werte vorhanden sind, verarbeitet die Middleware der Weiterleitungsheader die Header in umgekehrter Reihenfolge von rechts nach links. Der Standardwert von `ForwardLimit` ist `1` (eins), sodass nur der äußere rechte Wert aus den Headern verarbeitet wird, es sei denn, der Wert von `ForwardLimit` wird erhöht.

Die ursprüngliche IP-Remoteadresse der Anforderung muss mit einem Eintrag in den `KnownProxies`- oder `KnownNetworks`-Listen übereinstimmen, bevor weitergeleitete Header verarbeitet werden. Auf diese Weise wird das Headerspoofing begrenzt, da keine Weiterleitungen von nicht vertrauenswürdigen Proxys akzeptiert werden. Wenn ein unbekannter Proxy erkannt wird, gibt die Protokollierung die Adresse des Proxys an:

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

Im vorherigen Beispiel ist 10.0.0.100 ein Proxyserver. Wenn der Server ein vertrauenswürdiger Proxy ist, fügen Sie die IP-Adresse des Servers `KnownProxies` (oder ein vertrauenswürdiges Netzwerk `KnownNetworks`) in `Startup.ConfigureServices` hinzu. Weitere Informationen finden Sie im Abschnitt [Middleware für weitergeleitete Header: Optionen](#forwarded-headers-middleware-options).

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> Erlauben Sie nur vertrauenswürdigen Proxys und Netzwerken die Weiterleitung von Headern. Andernfalls sind Angriffe des Typs [IP-Spoofing](https://www.iplocation.net/ip-spoofing) möglich.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:host-and-deploy/web-farm>
* [Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability (Microsoft-Sicherheitsempfehlung CVE-2018-0787: Sicherheitsrisiko durch Rechteerweiterungen in ASP.NET Core)](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end
