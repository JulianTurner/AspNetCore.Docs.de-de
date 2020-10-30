---
title: Konfigurieren der Zertifikat Authentifizierung in ASP.net Core
author: blowdart
description: Erfahren Sie, wie Sie die Zertifikat Authentifizierung in ASP.net Core für IIS und HTTP.sys konfigurieren.
monikerRange: '>= aspnetcore-3.0'
ms.author: bdorrans
ms.date: 07/16/2020
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
uid: security/authentication/certauth
ms.openlocfilehash: 83525a4c1e87a60b57130c1bba14360c7d03f552
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061378"
---
# <a name="configure-certificate-authentication-in-aspnet-core"></a>Konfigurieren der Zertifikat Authentifizierung in ASP.net Core

`Microsoft.AspNetCore.Authentication.Certificate` enthält eine-Implementierung, die der [Zertifikat Authentifizierung](https://tools.ietf.org/html/rfc5246#section-7.4.4) für ASP.net Core ähnelt. Die Zertifikatsauthentifizierung erfolgt auf der TLS-Ebene, lange bevor sie überhaupt zum ASP.NET Core gelangt. Genauer ist dies ein Authentifizierungs Handler, der das Zertifikat überprüft und dann ein Ereignis liefert, bei dem Sie dieses Zertifikat in eine auflösen können `ClaimsPrincipal` . 

[Konfigurieren Sie den Server für die](#configure-your-server-to-require-certificates) Zertifikat Authentifizierung, also IIS, Kestrel, Azure-Web-Apps oder andere von Ihnen verwendete.

## <a name="proxy-and-load-balancer-scenarios"></a>Proxy-und Load Balancer-Szenarios

Die Zertifikat Authentifizierung ist ein Zustands behaftetes Szenario, das hauptsächlich verwendet wird, wenn ein Proxy oder ein Load Balancer den Datenverkehr zwischen Clients und Servern nicht Wenn ein Proxy oder ein Lasten Ausgleichs Modul verwendet wird, funktioniert die Zertifikat Authentifizierung nur, wenn der Proxy oder der Load Balancer:

* Behandelt die-Authentifizierung.
* Übergibt die Benutzer Authentifizierungsinformationen an die APP (z. b. in einem Anforderungs Header), die die Authentifizierungsinformationen verarbeitet.

Eine Alternative zur Zertifikat Authentifizierung in Umgebungen, in denen Proxys und Lasten Ausgleichs Module verwendet werden, ist Active Directory Verbund Dienste (AD FS) mit OpenID Connect (oidc).

## <a name="get-started"></a>Erste Schritte

Erwerben Sie ein HTTPS-Zertifikat, wenden Sie es an, und [Konfigurieren Sie den Server](#configure-your-server-to-require-certificates) für die Verwendung von Zertifikaten.

Fügen Sie in Ihrer Web-App einen Verweis auf das Paket [Microsoft. aspnetcore. Authentication. Certificate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Certificate) hinzu. Verwenden Sie dann in der- `Startup.ConfigureServices` Methode `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` mit den Optionen, und geben Sie einen Delegaten für `OnCertificateValidated` an, um eine ergänzende Validierung für das mit Anforderungen gesendete Client Zertifikat durchzuführen. Diese Informationen werden in ein `ClaimsPrincipal` -Objekt umgewandelt und für die-Eigenschaft festgelegt `context.Principal` .

Wenn die Authentifizierung fehlschlägt, gibt dieser Handler `403 (Forbidden)` wie erwartet eine Antwort zurück `401 (Unauthorized)` . Der Grund dafür ist, dass die Authentifizierung während der anfänglichen TLS-Verbindung stattfinden soll. Bis zum Zeitpunkt, an dem der Handler erreicht wird, ist es zu spät. Es gibt keine Möglichkeit, die Verbindung von einer anonymen Verbindung mit einem Zertifikat zu aktualisieren.

Fügen Sie außerdem `app.UseAuthentication();` die- `Startup.Configure` Methode hinzu. Andernfalls `HttpContext.User` wird nicht auf `ClaimsPrincipal` aus dem Zertifikat erstellt festgelegt. Zum Beispiel:

::: moniker range=">= aspnetcore-5.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
        .AddCertificate()
        // Adding an ICertificateValidationCache results in certificate auth caching the results.
        // The default implementation uses a memory cache.
        .AddCertificateCache();

    // All other service configuration
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();

    // All other app configuration
}
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
        .AddCertificate();

    // All other service configuration
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();

    // All other app configuration
}
```

::: moniker-end

Im vorangehenden Beispiel wird die Standardmethode zum Hinzufügen der Zertifikat Authentifizierung veranschaulicht. Der Handler erstellt einen Benutzer Prinzipal mithilfe der allgemeinen Zertifikat Eigenschaften.

## <a name="configure-certificate-validation"></a>Konfigurieren der Zertifikat Überprüfung

Der `CertificateAuthenticationOptions` Handler verfügt über einige integrierte Validierungen, bei denen es sich um die minimalen Überprüfungen handelt, die Sie für ein Zertifikat ausführen sollten. Jede dieser Einstellungen ist standardmäßig aktiviert.

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a>"Verkewedcertifipeetypes = verkettet", "selfsigned" oder "alle" (verkettet | Selfsigned)

Standardwert: `CertificateTypes.Chained`

Mit dieser Überprüfung wird überprüft, ob nur der geeignete Zertifikattyp zulässig ist. Wenn die APP selbst signierte Zertifikate verwendet, muss diese Option auf oder festgelegt werden `CertificateTypes.All` `CertificateTypes.SelfSigned` .

### <a name="validatecertificateuse"></a>Validatecertifi| euse

Standardwert: `true`

Mit dieser Überprüfung wird überprüft, ob das vom Client vorgelegte Zertifikat über die erweiterte Schlüssel Verwendung (EKU) der Client Authentifizierung oder über keine EKUs verfügt. Wie bei den Spezifikationen gesagt, werden alle EKUs als gültig eingestuft, wenn kein EKU angegeben wird.

### <a name="validatevalidityperiod"></a>Validatevalidityperiod

Standardwert: `true`

Diese Prüfung überprüft, ob das Zertifikat innerhalb seines Gültigkeits Zeitraums liegt. Bei jeder Anforderung stellt der Handler sicher, dass ein Zertifikat, das bei der Präsentation gültig war, während der aktuellen Sitzung nicht abgelaufen ist.

### <a name="revocationflag"></a>RevocationFlag

Standardwert: `X509RevocationFlag.ExcludeRoot`

Ein Flag, das angibt, welche Zertifikate in der Kette auf die Sperrung geprüft werden.

Sperr Überprüfungen werden nur ausgeführt, wenn das Zertifikat mit einem Stamm Zertifikat verkettet ist.

### <a name="revocationmode"></a>RevocationMode

Standardwert: `X509RevocationMode.Online`

Ein Flag, das angibt, wie Sperr Überprüfungen durchgeführt werden.

Das Angeben einer Online Überprüfung kann zu einer langen Verzögerung führen, während die Zertifizierungsstelle kontaktiert wird.

Sperr Überprüfungen werden nur ausgeführt, wenn das Zertifikat mit einem Stamm Zertifikat verkettet ist.

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a>Kann ich meine APP so konfigurieren, dass ein Zertifikat nur für bestimmte Pfade erforderlich ist?

Dies ist nicht möglich. Merken Sie sich, dass der Zertifikat Austausch durchgeführt wurde, bis der Start der HTTPS-Konversation durch den Server erfolgt ist, bevor die erste Anforderung über diese Verbindung empfangen wird. Daher ist es nicht möglich, den Bereich auf der Grundlage von Anforderungs Feldern festzustellen.

## <a name="handler-events"></a>Handlerereignisse

Der Handler hat zwei Ereignisse:

* `OnAuthenticationFailed`: Wird aufgerufen, wenn während der Authentifizierung eine Ausnahme auftritt und Sie reagieren können.
* `OnCertificateValidated`: Wird aufgerufen, nachdem das Zertifikat überprüft wurde, die Überprüfung bestanden und ein Standard Prinzipal erstellt wurde. Dieses Ereignis ermöglicht es Ihnen, ihre eigene Validierung auszuführen und den Prinzipal zu erweitern oder zu ersetzen. Beispiele hierfür sind:
  * Ermitteln, ob das Zertifikat den Diensten bekannt ist.
  * Erstellen eines eigenen Prinzipals. Betrachten Sie das folgende Beispiel in `Startup.ConfigureServices`:

```csharp
services.AddAuthentication(
    CertificateAuthenticationDefaults.AuthenticationScheme)
    .AddCertificate(options =>
    {
        options.Events = new CertificateAuthenticationEvents
        {
            OnCertificateValidated = context =>
            {
                var claims = new[]
                {
                    new Claim(
                        ClaimTypes.NameIdentifier, 
                        context.ClientCertificate.Subject,
                        ClaimValueTypes.String, 
                        context.Options.ClaimsIssuer),
                    new Claim(ClaimTypes.Name,
                        context.ClientCertificate.Subject,
                        ClaimValueTypes.String, 
                        context.Options.ClaimsIssuer)
                };

                context.Principal = new ClaimsPrincipal(
                    new ClaimsIdentity(claims, context.Scheme.Name));
                context.Success();

                return Task.CompletedTask;
            }
        };
    });
```

Wenn Sie feststellen, dass das eingehende Zertifikat die zusätzliche Überprüfung nicht erfüllt, können Sie `context.Fail("failure reason")` mit einem Fehler Grund anrufen.

Für echte Funktionen möchten Sie wahrscheinlich einen Dienst aufzurufen, der in der Abhängigkeitsinjektion registriert ist, der eine Verbindung mit einer Datenbank oder einem anderen Benutzerspeicher herstellt. Greifen Sie auf den Dienst zu, indem Sie den Kontext verwenden, der in ihren Delegaten Betrachten Sie das folgende Beispiel in `Startup.ConfigureServices`:

```csharp
services.AddAuthentication(
    CertificateAuthenticationDefaults.AuthenticationScheme)
    .AddCertificate(options =>
    {
        options.Events = new CertificateAuthenticationEvents
        {
            OnCertificateValidated = context =>
            {
                var validationService =
                    context.HttpContext.RequestServices
                        .GetRequiredService<ICertificateValidationService>();
                
                if (validationService.ValidateCertificate(
                    context.ClientCertificate))
                {
                    var claims = new[]
                    {
                        new Claim(
                            ClaimTypes.NameIdentifier, 
                            context.ClientCertificate.Subject, 
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer),
                        new Claim(
                            ClaimTypes.Name, 
                            context.ClientCertificate.Subject, 
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer)
                    };

                    context.Principal = new ClaimsPrincipal(
                        new ClaimsIdentity(claims, context.Scheme.Name));
                    context.Success();
                }                     

                return Task.CompletedTask;
            }
        };
    });
```

Konzeptionell ist die Validierung des Zertifikats ein Autorisierungs Problem. Das Hinzufügen einer Überprüfung (z. b. eines Ausstellers oder Fingerabdrucks in einer Autorisierungs Richtlinie) und nicht innerhalb von `OnCertificateValidated` ist durchaus akzeptabel.

## <a name="configure-your-server-to-require-certificates"></a>Konfigurieren des Servers für die Anforderung von Zertifikaten

### <a name="kestrel"></a>Kestrel

Konfigurieren Sie in *Program.cs* Kestrel wie folgt:

```csharp
public static void Main(string[] args)
{
    CreateHostBuilder(args).Build().Run();
}

public static IHostBuilder CreateHostBuilder(string[] args)
{
    return Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
            webBuilder.ConfigureKestrel(o =>
            {
                o.ConfigureHttpsDefaults(o => 
            o.ClientCertificateMode = 
                ClientCertificateMode.RequireCertificate);
            });
        });
}
```

> [!NOTE]
> Auf Endpunkte, die durch Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **vor** dem Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> erstellt werden, werden die Standardwerte nicht angewendet.

### <a name="iis"></a>IIS

Führen Sie im IIS-Manager die folgenden Schritte aus:

1. Wählen Sie auf der Registerkarte **Verbindungen** Ihre Website aus.
1. Doppelklicken Sie im Fenster " **Featureansicht** " auf die Option " **SSL-Einstellungen** ".
1. Aktivieren Sie das Kontrollkästchen **SSL erforderlich** , und aktivieren Sie im Abschnitt **Client Zertifikate** das Optionsfeld **erforderlich** .

![Client Zertifikat Einstellungen in IIS](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a>Azure und benutzerdefinierte Webproxys

Informationen zum Konfigurieren der Middleware für die Zertifikat Weiterleitung finden Sie in der [Dokumentation zu Host und](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) Bereitstellung.

### <a name="use-certificate-authentication-in-azure-web-apps"></a>Verwenden der Zertifikat Authentifizierung in Azure-Web-Apps

Für Azure ist keine Weiterleitungs Konfiguration erforderlich. Dies ist bereits in der Middleware für die Zertifikat Weiterleitung eingerichtet.

> [!NOTE]
> Hierfür ist es erforderlich, dass die certificateforwardingmiddleware vorhanden ist.

### <a name="use-certificate-authentication-in-custom-web-proxies"></a>Verwenden der Zertifikat Authentifizierung in benutzerdefinierten Webproxys

Die- `AddCertificateForwarding` Methode wird verwendet, um Folgendes anzugeben:

* Der Name des Client Headers.
* Wie das Zertifikat geladen werden soll (mithilfe der- `HeaderConverter` Eigenschaft).

In benutzerdefinierten Webproxys wird das Zertifikat beispielsweise als benutzerdefinierter Anforderungs Header übermittelt `X-SSL-CERT` . Um es zu verwenden, konfigurieren Sie die Zertifikat Weiterleitung in `Startup.ConfigureServices` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddCertificateForwarding(options =>
    {
        options.CertificateHeader = "X-SSL-CERT";
        options.HeaderConverter = (headerValue) =>
        {
            X509Certificate2 clientCertificate = null;
        
            if(!string.IsNullOrWhiteSpace(headerValue))
            {
                byte[] bytes = StringToByteArray(headerValue);
                clientCertificate = new X509Certificate2(bytes);
            }

            return clientCertificate;
        };
    });
}

private static byte[] StringToByteArray(string hex)
{
    int NumberChars = hex.Length;
    byte[] bytes = new byte[NumberChars / 2];

    for (int i = 0; i < NumberChars; i += 2)
    {
        bytes[i / 2] = Convert.ToByte(hex.Substring(i, 2), 16);
    }

    return bytes;
}
```

Die- `Startup.Configure` Methode fügt dann die Middleware hinzu. `UseCertificateForwarding` wird aufgerufen, bevor der Aufruf von `UseAuthentication` und erfolgt `UseAuthorization` :

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseRouting();

    app.UseCertificateForwarding();
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

Eine separate Klasse kann verwendet werden, um Validierungs Logik zu implementieren. Da in diesem Beispiel dasselbe selbst signierte Zertifikat verwendet wird, sollten Sie sicherstellen, dass nur Ihr Zertifikat verwendet werden kann. Überprüfen Sie, ob die Fingerabdrücke sowohl des Client Zertifikats als auch des Serverzertifikats Stimmen. andernfalls können alle Zertifikate verwendet werden, die für die Authentifizierung ausreichen. Dies wird in der- `AddCertificate` Methode verwendet. Sie können den Betreff oder den Aussteller hier auch überprüfen, wenn Sie zwischengeschaltete oder untergeordnete Zertifikate verwenden.

```csharp
using System.IO;
using System.Security.Cryptography.X509Certificates;

namespace AspNetCoreCertificateAuthApi
{
    public class MyCertificateValidationService
    {
        public bool ValidateCertificate(X509Certificate2 clientCertificate)
        {
            // Do not hardcode passwords in production code
            // Use thumbprint or key vault
            var cert = new X509Certificate2(
                Path.Combine("sts_dev_cert.pfx"), "1234");

            if (clientCertificate.Thumbprint == cert.Thumbprint)
            {
                return true;
            }

            return false;
        }
    }
}
```

#### <a name="implement-an-httpclient-using-a-certificate-and-the-httpclienthandler"></a>Implementieren eines httpclient mit einem Zertifikat und httpclienthandler

Das-Element `HttpClientHandler` könnte direkt im Konstruktor der-Klasse hinzugefügt werden `HttpClient` . Beim Erstellen von Instanzen von sollten Sie sorgfältig vorgehen `HttpClient` . `HttpClient`Sendet dann das Zertifikat mit jeder Anforderung.

```csharp
private async Task<JsonDocument> GetApiDataUsingHttpClientHandler()
{
    var cert = new X509Certificate2(Path.Combine(_environment.ContentRootPath, "sts_dev_cert.pfx"), "1234");
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(cert);
    var client = new HttpClient(handler);
     
    var request = new HttpRequestMessage()
    {
        RequestUri = new Uri("https://localhost:44379/api/values"),
        Method = HttpMethod.Get,
    };
    var response = await client.SendAsync(request);
    if (response.IsSuccessStatusCode)
    {
        var responseContent = await response.Content.ReadAsStringAsync();
        var data = JsonDocument.Parse(responseContent);
        return data;
    }
 
    throw new ApplicationException($"Status code: {response.StatusCode}, Error: {response.ReasonPhrase}");
}
```

#### <a name="implement-an-httpclient-using-a-certificate-and-a-named-httpclient-from-ihttpclientfactory"></a>Implementieren Sie einen httpclient mithilfe eines Zertifikats und eines Namens httpclient aus ihttpclientfactory. 

Im folgenden Beispiel wird ein Client Zertifikat `HttpClientHandler` mithilfe der-Eigenschaft des-Handlers zu einem hinzugefügt `ClientCertificates` . Dieser Handler kann dann mithilfe der-Methode in einer benannten Instanz von verwendet werden `HttpClient` `ConfigurePrimaryHttpMessageHandler` . Dies ist das Setup in `Startup.ConfigureServices` :

```csharp
var clientCertificate = 
    new X509Certificate2(
      Path.Combine(_environment.ContentRootPath, "sts_dev_cert.pfx"), "1234");
 
var handler = new HttpClientHandler();
handler.ClientCertificates.Add(clientCertificate);
 
services.AddHttpClient("namedClient", c =>
{
}).ConfigurePrimaryHttpMessageHandler(() => handler);
```

Der `IHttpClientFactory` kann dann verwendet werden, um die benannte Instanz mit dem Handler und dem Zertifikat zu erhalten. Die- `CreateClient` Methode mit dem Namen des Clients, der in der- `Startup` Klasse definiert ist, wird verwendet, um die-Instanz zu erhalten. Die HTTP-Anforderung kann bei Bedarf mithilfe des Clients gesendet werden.

```csharp
private readonly IHttpClientFactory _clientFactory;
 
public ApiService(IHttpClientFactory clientFactory)
{
    _clientFactory = clientFactory;
}
 
private async Task<JsonDocument> GetApiDataWithNamedClient()
{
    var client = _clientFactory.CreateClient("namedClient");
 
    var request = new HttpRequestMessage()
    {
        RequestUri = new Uri("https://localhost:44379/api/values"),
        Method = HttpMethod.Get,
    };
    var response = await client.SendAsync(request);
    if (response.IsSuccessStatusCode)
    {
        var responseContent = await response.Content.ReadAsStringAsync();
        var data = JsonDocument.Parse(responseContent);
        return data;
    }
 
    throw new ApplicationException($"Status code: {response.StatusCode}, Error: {response.ReasonPhrase}");
}
```

Wenn das richtige Zertifikat an den Server gesendet wird, werden die Daten zurückgegeben. Wenn kein Zertifikat oder falsches Zertifikat gesendet wird, wird ein HTTP 403-Statuscode zurückgegeben.

### <a name="create-certificates-in-powershell"></a>Erstellen von Zertifikaten in PowerShell

Das Erstellen der Zertifikate ist der schwierigste Teil beim Einrichten dieses Flows. Ein Stamm Zertifikat kann mithilfe des `New-SelfSignedCertificate` PowerShell-Cmdlets erstellt werden. Verwenden Sie beim Erstellen des Zertifikats ein sicheres Kennwort. Es ist wichtig, den- `KeyUsageProperty` Parameter und den- `KeyUsage` Parameter wie gezeigt hinzuzufügen.

#### <a name="create-root-ca"></a>Stamm Zertifizierungsstelle erstellen

```powershell
New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath root_ca_dev_damienbod.crt
```

> [!NOTE]
> Der `-DnsName` Parameterwert muss mit dem Bereitstellungs Ziel der APP identisch sein. Beispiel: "localhost" für die Entwicklung.

#### <a name="install-in-the-trusted-root"></a>Installieren des vertrauenswürdigen Stamms

Das Stamm Zertifikat muss auf Ihrem Host System als vertrauenswürdig eingestuft werden. Ein Stamm Zertifikat, das nicht von einer Zertifizierungsstelle erstellt wurde, ist standardmäßig nicht vertrauenswürdig. Der folgende Link erläutert, wie dies unter Windows erreicht werden kann:

https://social.msdn.microsoft.com/Forums/SqlServer/5ed119ef-1704-4be4-8a4f-ef11de7c8f34/a-certificate-chain-processed-but-terminated-in-a-root-certificate-which-is-not-trusted-by-the

#### <a name="intermediate-certificate"></a>Zwischenzertifikat

Ein zwischen Zertifikat kann nun aus dem Stamm Zertifikat erstellt werden. Dies ist für alle Anwendungsfälle nicht erforderlich, aber Sie müssen möglicherweise viele Zertifikate erstellen oder Gruppen von Zertifikaten aktivieren bzw. deaktivieren. Der- `TextExtension` Parameter ist erforderlich, um die Pfadlänge in den grundeinschränkungen des Zertifikats festzulegen.

Das zwischen Zertifikat kann dann dem vertrauenswürdigen zwischen Zertifikat im Windows-Host System hinzugefügt werden.

```powershell
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint of the root..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "intermediate_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "intermediate_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\intermediate_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath intermediate_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-intermediate-certificate"></a>Untergeordnetes Zertifikat aus zwischen Zertifikat erstellen

Ein untergeordnetes Zertifikat kann aus dem zwischen Zertifikat erstellt werden. Dies ist die Endentität, und es müssen nicht mehr untergeordnete Zertifikate erstellt werden.

```powershell
$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the Intermediate certificate..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-root-certificate"></a>Erstellen des untergeordneten Zertifikats aus dem Stamm Zertifikat

Ein untergeordnetes Zertifikat kann auch direkt aus dem Stamm Zertifikat erstellt werden. 

```powershell
$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the root cert..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="example-root---intermediate-certificate---certificate"></a>Beispiel für ein root-zwischen Zertifikat-Zertifikat

```powershell
$mypwdroot = ConvertTo-SecureString -String "1234" -Force -AsPlainText
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

Get-ChildItem -Path cert:\localMachine\my\0C89639E4E2998A93E423F919B36D4009A0F9991 | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwdroot

Export-Certificate -Cert cert:\localMachine\my\0C89639E4E2998A93E423F919B36D4009A0F9991 -FilePath root_ca_dev_damienbod.crt

$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\0C89639E4E2998A93E423F919B36D4009A0F9991 )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\BA9BF91ED35538A01375EFC212A2F46104B33A44 | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\BA9BF91ED35538A01375EFC212A2F46104B33A44 -FilePath child_a_dev_damienbod.crt

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\BA9BF91ED35538A01375EFC212A2F46104B33A44 )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_b_from_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_b_from_a_dev_damienbod.com" 

Get-ChildItem -Path cert:\localMachine\my\141594A0AE38CBBECED7AF680F7945CD51D8F28A | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_b_from_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\141594A0AE38CBBECED7AF680F7945CD51D8F28A -FilePath child_b_from_a_dev_damienbod.crt
```

Wenn Sie die Stamm-, zwischen-oder untergeordneten Zertifikate verwenden, können die Zertifikate nach Bedarf mithilfe des Fingerabdrucks oder PublicKey überprüft werden.

```csharp
using System.Collections.Generic;
using System.IO;
using System.Security.Cryptography.X509Certificates;

namespace AspNetCoreCertificateAuthApi
{
    public class MyCertificateValidationService 
    {
        public bool ValidateCertificate(X509Certificate2 clientCertificate)
        {
            return CheckIfThumbprintIsValid(clientCertificate);
        }

        private bool CheckIfThumbprintIsValid(X509Certificate2 clientCertificate)
        {
            var listOfValidThumbprints = new List<string>
            {
                "141594A0AE38CBBECED7AF680F7945CD51D8F28A",
                "0C89639E4E2998A93E423F919B36D4009A0F9991",
                "BA9BF91ED35538A01375EFC212A2F46104B33A44"
            };

            if (listOfValidThumbprints.Contains(clientCertificate.Thumbprint))
            {
                return true;
            }

            return false;
        }
    }
}
```

<a name="occ"></a>

::: moniker range=">= aspnetcore-5.0"

## <a name="certificate-validation-caching"></a>Zwischenspeichern der Zertifikat Überprüfung

ASP.net Core 5,0 und höhere Versionen unterstützen die Möglichkeit, das Zwischenspeichern von Validierungs Ergebnissen zu aktivieren. Durch die Zwischenspeicherung wird die Leistung der Zertifikat Authentifizierung erheblich verbessert, da die Validierung ein kostspieliger Vorgang ist.

Standardmäßig wird die Zwischenspeicherung von der Zertifikat Authentifizierung deaktiviert. Zum Aktivieren der Zwischenspeicherung wenden Sie in Folgendes an `AddCertificateCache` `Startup.ConfigureServices` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
            .AddCertificate()
            .AddCertificateCache(options =>
            {
                options.CacheSize = 1024;
                options.CacheEntryExpiration = TimeSpan.FromMinutes(2);
            });
}
```

Die standardmäßige Cache Implementierung speichert die Ergebnisse im Arbeitsspeicher. Sie können Ihren eigenen Cache bereitstellen, indem `ICertificateValidationCache` Sie ihn mit Abhängigkeitsinjektion implementieren und registrieren. Beispiel: `services.AddSingleton<ICertificateValidationCache, YourCache>()`.

::: moniker-end

## <a name="optional-client-certificates"></a>Optionale Client Zertifikate

Dieser Abschnitt enthält Informationen für apps, die eine Teilmenge der APP mit einem Zertifikat schützen müssen. Beispielsweise sind für eine Razor Seite oder einen Controller in der APP möglicherweise Client Zertifikate erforderlich. Dies stellt Herausforderungen als Client Zertifikate dar:
  
* Ist eine TLS-Funktion und keine HTTP-Funktion.
* Werden pro Verbindung ausgehandelt und müssen zu Beginn der Verbindung ausgehandelt werden, bevor HTTP-Daten verfügbar sind. Zu Beginn der Verbindung ist nur die Servernamensanzeige (SNI) &dagger; bekannt. Die Client-und Server Zertifikate werden vor der ersten Anforderung einer Verbindung ausgehandelt, und Anforderungen können in der Regel nicht erneut ausgehandelt werden.

Die TLS-Aushandlung war eine alte Möglichkeit zum Implementieren Optionaler Client Zertifikate. Dies wird aus folgenden Gründen nicht mehr empfohlen:
- In HTTP/1.1 könnte das erneute aushandeln während einer POST-Anforderung zu einem Deadlock führen, bei dem der Anforderungs Text das TCP-Fenster ausgefüllt hat und die erneut ausgehandelten Pakete nicht empfangen werden können.
- HTTP/2 [verbietet](https://tools.ietf.org/html/rfc7540#section-9.2.1) die erneute Aushandlung explizit.
- TLS 1,3 hat die Unterstützung für die erneute Aushandlung [entfernt](https://tools.ietf.org/html/rfc8740#section-1) .

ASP.net Core 5 Preview 7 und höher bietet eine komfortable Unterstützung für optionale Client Zertifikate. Weitere Informationen finden Sie im [Beispiel optionale Zertifikate](https://github.com/dotnet/aspnetcore/tree/9ce4a970a21bace3fb262da9591ed52359309592/src/Security/Authentication/Certificate/samples/Certificate.Optional.Sample).

Der folgende Ansatz unterstützt optionale Client Zertifikate:

* Richten Sie eine Bindung für die Domäne und die Unterdomäne ein:
  * Richten Sie z. b. Bindungen für `contoso.com` und ein `myClient.contoso.com` . Für den `contoso.com` Host ist kein Client Zertifikat erforderlich `myClient.contoso.com` .
  * Weitere Informationen finden Sie unter
    * [Kestrel](/fundamentals/servers/kestrel):
      * [ListenOptions.UseHttps](xref:fundamentals/servers/kestrel#listenoptionsusehttps)
      * <xref:Microsoft.AspNetCore.Server.Kestrel.Https.HttpsConnectionAdapterOptions.ClientCertificateMode>
      * Hinweis Kestrel unterstützt derzeit nicht mehrere TLS-Konfigurationen für eine Bindung. Sie benötigen zwei Bindungen mit eindeutigen IPS oder Ports. Siehe https://github.com/dotnet/runtime/issues/31097.
    * IIS
      * [Hosting von IIS](xref:host-and-deploy/iis/index#create-the-iis-site)
      * [Konfigurieren der Sicherheit für IIS](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#configure-ssl-settings-2)
    * Http.Sys: [Konfigurieren von Windows Server](xref:fundamentals/servers/httpsys#configure-windows-server)
* Für Anforderungen an die Web-App, für die ein Client Zertifikat erforderlich ist und keines vorhanden ist:
  * Leiten Sie mithilfe der geschützten Unterdomäne des Client Zertifikats eine Umleitung zur gleichen Seite ein.
  * Beispielsweise können Sie zu umleiten `myClient.contoso.com/requestedPage` . Da es sich bei der Anforderung an um `myClient.contoso.com/requestedPage` einen anderen Hostnamen als handelt `contoso.com/requestedPage` , stellt der Client eine andere Verbindung her, und das Client Zertifikat wird bereitgestellt.
  * Weitere Informationen finden Sie unter <xref:security/authorization/introduction>.

In [diesem GitHub-Diskussions](https://github.com/dotnet/AspNetCore.Docs/issues/18720) Problem können Sie Fragen, Kommentare und anderes Feedback zu optionalen Client Zertifikaten hinterlassen.

&dagger; Servernamensanzeige (SNI) ist eine TLS-Erweiterung zum Einbeziehen einer virtuellen Domäne als Teil der SSL-Aushandlung. Dies bedeutet, dass der virtuelle Domänen Name oder ein Hostname zur Identifizierung des Netzwerk Endpunkts verwendet werden kann.
