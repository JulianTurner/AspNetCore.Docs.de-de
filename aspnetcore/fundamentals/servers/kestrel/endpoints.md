---
title: Konfigurieren von Endpunkten für den ASP.NET Core-Kestrel-Webserver
author: rick-anderson
description: Hier erhalten Sie Informationen zum Konfigurieren von Endpunkten mit Kestrel, dem plattformübergreifenden Webserver für ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel/endpoints
ms.openlocfilehash: 780250feab456fa3eedee4e023c9bc774e748291
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253848"
---
# <a name="configure-endpoints-for-the-aspnet-core-kestrel-web-server"></a><span data-ttu-id="788db-103">Konfigurieren von Endpunkten für den ASP.NET Core-Kestrel-Webserver</span><span class="sxs-lookup"><span data-stu-id="788db-103">Configure endpoints for the ASP.NET Core Kestrel web server</span></span>

<span data-ttu-id="788db-104">Standardmäßig wird ASP.NET Core an Folgendes gebunden:</span><span class="sxs-lookup"><span data-stu-id="788db-104">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="788db-105">`https://localhost:5001` (wenn ein lokales Entwicklungszertifikat vorhanden ist)</span><span class="sxs-lookup"><span data-stu-id="788db-105">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="788db-106">Verwenden Sie Folgendes zum Angeben der URLs:</span><span class="sxs-lookup"><span data-stu-id="788db-106">Specify URLs using the:</span></span>

* <span data-ttu-id="788db-107">Die Umgebungsvariable `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="788db-107">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="788db-108">Das Befehlszeilenargument `--urls`</span><span class="sxs-lookup"><span data-stu-id="788db-108">`--urls` command-line argument.</span></span>
* <span data-ttu-id="788db-109">Den Hostkonfigurationsschlüssel `urls`</span><span class="sxs-lookup"><span data-stu-id="788db-109">`urls` host configuration key.</span></span>
* <span data-ttu-id="788db-110">Die Erweiterungsmethode `UseUrls`</span><span class="sxs-lookup"><span data-stu-id="788db-110">`UseUrls` extension method.</span></span>

<span data-ttu-id="788db-111">Der Wert, der mit diesen Ansätzen angegeben wird, kann mindestens ein HTTP- oder HTTPS-Endpunkt sein (HTTPS wenn ein Standardzertifikat verfügbar ist).</span><span class="sxs-lookup"><span data-stu-id="788db-111">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="788db-112">Konfigurieren Sie den Wert als eine durch Semikolons getrennte Liste (z.B. `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="788db-112">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="788db-113">Weitere Informationen zu diesen Ansätzen finden Sie unter [Server-URLs](xref:fundamentals/host/web-host#server-urls) und [Außerkraftsetzen der Konfiguration](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="788db-113">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="788db-114">Ein Entwicklungszertifikat wird erstellt:</span><span class="sxs-lookup"><span data-stu-id="788db-114">A development certificate is created:</span></span>

* <span data-ttu-id="788db-115">Bei Installation des [.NET SDK](/dotnet/core/sdk)</span><span class="sxs-lookup"><span data-stu-id="788db-115">When the [.NET SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="788db-116">Das [dev-cert-Tool](xref:aspnetcore-2.1#https) wird zum Erstellen eines Zertifikats verwendet.</span><span class="sxs-lookup"><span data-stu-id="788db-116">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="788db-117">Einige Browser erfordern, dass Sie die explizite Berechtigung erteilen, dem lokalen Entwicklungszertifikat zu vertrauen.</span><span class="sxs-lookup"><span data-stu-id="788db-117">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="788db-118">Projektvorlagen konfigurieren Apps, damit sie standardmäßig auf HTTPS ausgeführt werden und die [HTTPS-Umleitung und HSTS-Unterstützung](xref:security/enforcing-ssl) enthalten.</span><span class="sxs-lookup"><span data-stu-id="788db-118">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="788db-119">Rufen Sie die <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A>- oder <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A>-Methode unter <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> auf, um URL-Präfixe und Ports für Kestrel zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="788db-119">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="788db-120">`UseUrls`, das Befehlszeilenargument `--urls`, der Hostkonfigurationsschlüssel `urls` und die Umgebungsvariable `ASPNETCORE_URLS` funktionieren ebenfalls, verfügen jedoch über Einschränkungen, die im Verlauf dieses Abschnitts erläutert werden (Ein Standardzertifikat muss für die HTTPS-Endpunktkonfiguration verfügbar sein).</span><span class="sxs-lookup"><span data-stu-id="788db-120">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="788db-121">`KestrelServerOptions`-Konfiguration:</span><span class="sxs-lookup"><span data-stu-id="788db-121">`KestrelServerOptions` configuration:</span></span>

## <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="788db-122">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="788db-122">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="788db-123">Gibt die Konfiguration von `Action` zum Ausführen von jedem angegebenen Endpunkt an.</span><span class="sxs-lookup"><span data-stu-id="788db-123">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="788db-124">Mehrmalige Aufrufe von `ConfigureEndpointDefaults` ersetzen vorherige Instanzen von `Action` mit der zuletzt angegebenen `Action`.</span><span class="sxs-lookup"><span data-stu-id="788db-124">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });
});
```

> [!NOTE]
> <span data-ttu-id="788db-125">Auf Endpunkte, die durch Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> **vor** dem Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults%2A> erstellt werden, werden die Standardwerte nicht angewendet.</span><span class="sxs-lookup"><span data-stu-id="788db-125">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults%2A> won't have the defaults applied.</span></span>

## <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="788db-126">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="788db-126">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="788db-127">Gibt die Konfiguration von `Action` zum Ausführen von jedem HTTPS-Endpunkt an.</span><span class="sxs-lookup"><span data-stu-id="788db-127">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="788db-128">Mehrmalige Aufrufe von `ConfigureHttpsDefaults` ersetzen vorherige Instanzen von `Action` mit der zuletzt angegebenen `Action`.</span><span class="sxs-lookup"><span data-stu-id="788db-128">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        // certificate is an X509Certificate2
        listenOptions.ServerCertificate = certificate;
    });
});
```

> [!NOTE]
> <span data-ttu-id="788db-129">Auf Endpunkte, die durch Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> **vor** dem Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults%2A> erstellt werden, werden die Standardwerte nicht angewendet.</span><span class="sxs-lookup"><span data-stu-id="788db-129">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults%2A> won't have the defaults applied.</span></span>

## <a name="configureiconfiguration"></a><span data-ttu-id="788db-130">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="788db-130">Configure(IConfiguration)</span></span>

<span data-ttu-id="788db-131">Erstellt ein Konfigurationsladeprogramm für das Einrichten von Kestrel, was <xref:Microsoft.Extensions.Configuration.IConfiguration> als Eingabe erfordert.</span><span class="sxs-lookup"><span data-stu-id="788db-131">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="788db-132">Die Konfiguration muss auf den Konfigurationsabschnitt für Kestrel festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="788db-132">The configuration must be scoped to the configuration section for Kestrel.</span></span>

<span data-ttu-id="788db-133">`CreateDefaultBuilder` ruft `Configure(context.Configuration.GetSection("Kestrel"))` standardmäßig zum Laden der Kestrel-Konfiguration auf.</span><span class="sxs-lookup"><span data-stu-id="788db-133">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },
      "Https": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    }
  }
}
```

## <a name="listenoptionsusehttps"></a><span data-ttu-id="788db-134">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="788db-134">ListenOptions.UseHttps</span></span>

<span data-ttu-id="788db-135">Konfiguriert Kestrel zur Verwendung von HTTPS.</span><span class="sxs-lookup"><span data-stu-id="788db-135">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="788db-136">`ListenOptions.UseHttps`-Erweiterungen:</span><span class="sxs-lookup"><span data-stu-id="788db-136">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="788db-137">`UseHttps`: Hiermit wird Kestrel zur Verwendung von HTTPS mit dem Standardzertifikat konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="788db-137">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="788db-138">Löst eine Ausnahme aus, wenn kein Standardzertifikat konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="788db-138">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="788db-139">`ListenOptions.UseHttps`-Parameter:</span><span class="sxs-lookup"><span data-stu-id="788db-139">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="788db-140">`filename` entspricht dem Pfad und Dateinamen einer Zertifikatdatei relativ zu dem Verzeichnis, das die Inhaltsdateien der App enthält.</span><span class="sxs-lookup"><span data-stu-id="788db-140">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="788db-141">`password` ist das für den Zugriff auf die X.509-Zertifikatsdaten erforderliche Kennwort.</span><span class="sxs-lookup"><span data-stu-id="788db-141">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="788db-142">`configureOptions` ist eine `Action` zum Konfigurieren von `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="788db-142">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="788db-143">Gibt `ListenOptions` zurück.</span><span class="sxs-lookup"><span data-stu-id="788db-143">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="788db-144">`storeName` ist der Zertifikatspeicher, aus dem das Zertifikat geladen wird.</span><span class="sxs-lookup"><span data-stu-id="788db-144">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="788db-145">`subject` ist der Name des Antragstellers für das Zertifikat.</span><span class="sxs-lookup"><span data-stu-id="788db-145">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="788db-146">`allowInvalid` gibt an, ob ungültige Zertifikate berücksichtigt werden sollten, z.B. selbstsignierte Zertifikate.</span><span class="sxs-lookup"><span data-stu-id="788db-146">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="788db-147">`location` ist der Speicherort, aus dem das Zertifikat geladen wird.</span><span class="sxs-lookup"><span data-stu-id="788db-147">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="788db-148">`serverCertificate` ist das X.509-Zertifikat.</span><span class="sxs-lookup"><span data-stu-id="788db-148">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="788db-149">In der Produktion muss HTTPS explizit konfiguriert sein.</span><span class="sxs-lookup"><span data-stu-id="788db-149">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="788db-150">Zumindest muss ein Standardzertifikat angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="788db-150">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="788db-151">Die im Folgenden beschriebenen unterstützten Konfigurationen:</span><span class="sxs-lookup"><span data-stu-id="788db-151">Supported configurations described next:</span></span>

* <span data-ttu-id="788db-152">Keine Konfiguration</span><span class="sxs-lookup"><span data-stu-id="788db-152">No configuration</span></span>
* <span data-ttu-id="788db-153">Ersetzen des Standardzertifikats aus der Konfiguration</span><span class="sxs-lookup"><span data-stu-id="788db-153">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="788db-154">Ändern des Standards im Code</span><span class="sxs-lookup"><span data-stu-id="788db-154">Change the defaults in code</span></span>

### <a name="no-configuration"></a><span data-ttu-id="788db-155">Keine Konfiguration</span><span class="sxs-lookup"><span data-stu-id="788db-155">No configuration</span></span>

<span data-ttu-id="788db-156">Kestrel überwacht `http://localhost:5000` und `https://localhost:5001` (wenn ein Standardzertifikat verfügbar ist).</span><span class="sxs-lookup"><span data-stu-id="788db-156">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

### <a name="replace-the-default-certificate-from-configuration"></a><span data-ttu-id="788db-157">Ersetzen des Standardzertifikats aus der Konfiguration</span><span class="sxs-lookup"><span data-stu-id="788db-157">Replace the default certificate from configuration</span></span>

<span data-ttu-id="788db-158">Ein Standardkonfigurationsschema für HTTPS-App-Einstellungen ist für Kestrel verfügbar.</span><span class="sxs-lookup"><span data-stu-id="788db-158">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="788db-159">Konfigurieren Sie mehrere Endpunkte, einschließlich der zu verwendenden URLs und Zertifikate aus einer Datei auf dem Datenträger oder einem Zertifikatspeicher.</span><span class="sxs-lookup"><span data-stu-id="788db-159">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="788db-160">Im folgenden Beispiel für *appsettings.json* gilt:</span><span class="sxs-lookup"><span data-stu-id="788db-160">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="788db-161">Legen Sie `AllowInvalid` auf `true` fest, um die Verwendung von ungültigen Zertifikaten zu erlauben (z.B. selbstsignierte Zertifikate).</span><span class="sxs-lookup"><span data-stu-id="788db-161">Set `AllowInvalid` to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="788db-162">Jeder HTTPS-Endpunkt, der kein Zertifikat angibt (im folgenden Beispiel `HttpsDefaultCert`), greift auf das unter `Certificates` > `Default` festgelegte Zertifikat oder das Entwicklungszertifikat zurück.</span><span class="sxs-lookup"><span data-stu-id="788db-162">Any HTTPS endpoint that doesn't specify a certificate (`HttpsDefaultCert` in the example that follows) falls back to the cert defined under `Certificates` > `Default` or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },
      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },
      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },
      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },
      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="788db-163">Alternativ zur Verwendung von `Path` und `Password` für alle Zertifikatknoten können Sie das Zertifikat mithilfe von Zertifikatspeicherfeldern angeben.</span><span class="sxs-lookup"><span data-stu-id="788db-163">An alternative to using `Path` and `Password` for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="788db-164">Das Zertifikat unter `Certificates` > `Default` kann beispielweise wie folgt angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="788db-164">For example, the `Certificates` > `Default` certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="788db-165">Schema-Hinweise:</span><span class="sxs-lookup"><span data-stu-id="788db-165">Schema notes:</span></span>

* <span data-ttu-id="788db-166">Bei den Namen der Endpunkte wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="788db-166">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="788db-167">Zum Beispiel sind `HTTPS` und `Https` gültig.</span><span class="sxs-lookup"><span data-stu-id="788db-167">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="788db-168">Der Parameter `Url` ist für jeden Endpunkt erforderlich.</span><span class="sxs-lookup"><span data-stu-id="788db-168">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="788db-169">Das Format für diesen Parameter ist identisch mit dem allgemeinen Konfigurationsparameter `Urls`, mit der Ausnahme, dass er auf einen einzelnen Wert begrenzt ist.</span><span class="sxs-lookup"><span data-stu-id="788db-169">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="788db-170">Diese Endpunkte ersetzen die in der allgemeinen `Urls`-Konfiguration festgelegten Endpunkte, anstatt zu ihnen hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="788db-170">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="788db-171">Endpunkte, die über `Listen` in Code definiert werden, werden den im Konfigurationsabschnitt definierten Endpunkten hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="788db-171">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="788db-172">Der `Certificate`-Abschnitt ist optional.</span><span class="sxs-lookup"><span data-stu-id="788db-172">The `Certificate` section is optional.</span></span> <span data-ttu-id="788db-173">Wenn der `Certificate`-Abschnitt nicht angegeben ist, werden die in früheren Szenarios definierten Standardwerte verwendet.</span><span class="sxs-lookup"><span data-stu-id="788db-173">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="788db-174">Wenn keine Standardwerte verfügbar sind, löst der Server eine Ausnahme aus und startet nicht.</span><span class="sxs-lookup"><span data-stu-id="788db-174">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="788db-175">Der `Certificate`-Abschnitt unterstützt die Zertifikate `Path`&ndash;`Password` und `Subject`&ndash;`Store` (Antragsteller–Speicher).</span><span class="sxs-lookup"><span data-stu-id="788db-175">The `Certificate` section supports both `Path`&ndash;`Password` and `Subject`&ndash;`Store` certificates.</span></span>
* <span data-ttu-id="788db-176">Auf diese Weise kann eine beliebige Anzahl von Endpunkten definiert werden, solange Sie keine Portkonflikte verursachen.</span><span class="sxs-lookup"><span data-stu-id="788db-176">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="788db-177">`options.Configure(context.Configuration.GetSection("{SECTION}"))` gibt `KestrelConfigurationLoader` mit der Methode `.Endpoint(string name, listenOptions => { })` zurück, die dazu verwendet werden kann, die Einstellungen eines Endpunkts zu ergänzen:</span><span class="sxs-lookup"><span data-stu-id="788db-177">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
webBuilder.UseKestrel((context, serverOptions) =>
{
    serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
        .Endpoint("HTTPS", listenOptions =>
        {
            listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
        });
});
```

<span data-ttu-id="788db-178">Auf `KestrelServerOptions.ConfigurationLoader` kann direkt zugegriffen werden, um die Iteration auf dem vorhandenen Ladeprogramm fortzusetzen, etwa auf dem von <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> bereitgestellten Ladeprogramm.</span><span class="sxs-lookup"><span data-stu-id="788db-178">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>.</span></span>

* <span data-ttu-id="788db-179">Der Konfigurationsabschnitt ist für jeden Endpunkt in den Optionen der Methode `Endpoint` verfügbar, sodass benutzerdefinierte Einstellungen gelesen werden können.</span><span class="sxs-lookup"><span data-stu-id="788db-179">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="788db-180">Mehrere Konfigurationen können durch erneutes Aufrufen von `options.Configure(context.Configuration.GetSection("{SECTION}"))` mit einem anderen Abschnitt geladen werden.</span><span class="sxs-lookup"><span data-stu-id="788db-180">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="788db-181">Sofern `Load` nicht in einer vorherigen Instanz explizit aufgerufen wird, wird nur die letzte Konfiguration verwendet.</span><span class="sxs-lookup"><span data-stu-id="788db-181">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="788db-182">Das Metapaket ruft `Load` nicht auf, sodass der Abschnitt mit der Standardkonfiguration ersetzt werden kann.</span><span class="sxs-lookup"><span data-stu-id="788db-182">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="788db-183">`KestrelConfigurationLoader` spiegelt die API-Familie `Listen` von `KestrelServerOptions` als `Endpoint`-Überladungen, weshalb Code und Konfigurationsendpunkte am selben Ort konfiguriert werden können.</span><span class="sxs-lookup"><span data-stu-id="788db-183">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="788db-184">Diese Überladungen verwenden keine Namen und nutzen nur Standardeinstellungen aus der Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="788db-184">These overloads don't use names and only consume default settings from configuration.</span></span>

### <a name="change-the-defaults-in-code"></a><span data-ttu-id="788db-185">Ändern des Standards im Code</span><span class="sxs-lookup"><span data-stu-id="788db-185">Change the defaults in code</span></span>

<span data-ttu-id="788db-186">`ConfigureEndpointDefaults` und `ConfigureHttpsDefaults` können zum Ändern der Standardeinstellungen für `ListenOptions` und `HttpsConnectionAdapterOptions` verwendet werden, einschließlich der Standardzertifikate, die im vorherigen Szenario festgelegt wurden.</span><span class="sxs-lookup"><span data-stu-id="788db-186">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="788db-187">`ConfigureEndpointDefaults` und `ConfigureHttpsDefaults` sollten aufgerufen werden, bevor Endpunkte konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="788db-187">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });

    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.SslProtocols = SslProtocols.Tls12;
    });
});
```

## <a name="configure-endpoints-using-server-name-indication"></a><span data-ttu-id="788db-188">Konfigurieren von Endpunkten mithilfe der Servernamensanzeige</span><span class="sxs-lookup"><span data-stu-id="788db-188">Configure endpoints using Server Name Indication</span></span>

<span data-ttu-id="788db-189">Die [Servernamensanzeige (SNI)](https://tools.ietf.org/html/rfc6066#section-3) kann zum Hosten mehrerer Domänen auf der gleichen IP-Adresse und dem gleichen Port verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="788db-189">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="788db-190">Damit die Servernamensanzeige funktioniert, sendet der Client während des TLS-Handshakes den Hostnamen für die sichere Sitzung an den Server, sodass der Server das richtige Zertifikat bereitstellen kann.</span><span class="sxs-lookup"><span data-stu-id="788db-190">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="788db-191">Der Client verwendet das beigestellte Zertifikat für die verschlüsselte Kommunikation mit dem Server während der sicheren Sitzung nach dem TLS-Handshake.</span><span class="sxs-lookup"><span data-stu-id="788db-191">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="788db-192">Kestrel unterstützt die Servernamensanzeige über den `ServerCertificateSelector`-Rückruf.</span><span class="sxs-lookup"><span data-stu-id="788db-192">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="788db-193">Der Rückruf wird für jede Verbindung einmal aufgerufen, um der App zu ermöglichen, den Hostnamen zu überprüfen und das entsprechende Zertifikat auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="788db-193">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span> <span data-ttu-id="788db-194">Der folgende Rückrufcode kann im `ConfigureWebHostDefaults`-Methodenaufruf der *Program.cs*-Datei eines Projekts verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="788db-194">The following callback code can be used in the `ConfigureWebHostDefaults` method call of a project's *Program.cs* file:</span></span>

```csharp
//using System.Security.Cryptography.X509Certificates;
//using Microsoft.AspNetCore.Server.Kestrel.Https;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ListenAnyIP(5005, listenOptions =>
    {
        listenOptions.UseHttps(httpsOptions =>
        {
            var localhostCert = CertificateLoader.LoadFromStoreCert(
                "localhost", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var exampleCert = CertificateLoader.LoadFromStoreCert(
                "example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var subExampleCert = CertificateLoader.LoadFromStoreCert(
                "sub.example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var certs = new Dictionary<string, X509Certificate2>(StringComparer.OrdinalIgnoreCase)
            {
                { "localhost", localhostCert },
                { "example.com", exampleCert },
                { "sub.example.com", subExampleCert },
            };            

            httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
            {
                if (name != null && certs.TryGetValue(name, out var cert))
                {
                    return cert;
                }

                return exampleCert;
            };
        });
    });
});
```

<span data-ttu-id="788db-195">Für die Unterstützung der Servernamensanzeige benötigen Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="788db-195">SNI support requires:</span></span>

* <span data-ttu-id="788db-196">Wird auf dem Zielframework `netcoreapp2.1` oder höher ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="788db-196">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="788db-197">In `net461` oder höher, wird der Rückruf aufgerufen, `name` ist aber immer `null`.</span><span class="sxs-lookup"><span data-stu-id="788db-197">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="788db-198">`name` ist auch `null`, wenn der Client den Hostnamenparameter nicht im TLS-Handshake angibt.</span><span class="sxs-lookup"><span data-stu-id="788db-198">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="788db-199">Alle Websites werden in derselben Kestrel-Instanz ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="788db-199">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="788db-200">Kestrel unterstützt ohne Reverseproxy keine gemeinsame IP-Adresse und keinen gemeinsamen Port für mehrere Instanzen.</span><span class="sxs-lookup"><span data-stu-id="788db-200">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

## <a name="connection-logging"></a><span data-ttu-id="788db-201">Verbindungsprotokollierung</span><span class="sxs-lookup"><span data-stu-id="788db-201">Connection logging</span></span>

<span data-ttu-id="788db-202">Rufen Sie <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging%2A> auf, um Protokolle auf Debugebene für die Kommunikation auf Byteebene für eine Verbindung auszugeben.</span><span class="sxs-lookup"><span data-stu-id="788db-202">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging%2A> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="788db-203">Die Verbindungsprotokollierung ist beim Beheben von Problemen bei der Low-Level-Kommunikation hilfreich, wie z. B. bei der TLS-Verschlüsselung und bei Proxys.</span><span class="sxs-lookup"><span data-stu-id="788db-203">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="788db-204">Wenn `UseConnectionLogging` vor `UseHttps` platziert wird, wird der verschlüsselte Datenverkehr protokolliert.</span><span class="sxs-lookup"><span data-stu-id="788db-204">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="788db-205">Wenn `UseConnectionLogging` nach `UseHttps` platziert wird, wird der entschlüsselte Datenverkehr protokolliert.</span><span class="sxs-lookup"><span data-stu-id="788db-205">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span> <span data-ttu-id="788db-206">Hierbei handelt es sich um integrierte [Verbindungsmiddleware](#connection-middleware).</span><span class="sxs-lookup"><span data-stu-id="788db-206">This is built-in [Connection Middleware](#connection-middleware).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

## <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="788db-207">Binden an einen TCP-Socket</span><span class="sxs-lookup"><span data-stu-id="788db-207">Bind to a TCP socket</span></span>

<span data-ttu-id="788db-208">Die <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A>-Methode wird an ein TCP-Socket gebunden, und ein Lambdaausdruck einer Option lässt die Konfiguration des X.509-Zertifikats zu:</span><span class="sxs-lookup"><span data-stu-id="788db-208">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="788db-209">Im Beispiel wird HTTPS für einen Endpunkt mit <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="788db-209">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="788db-210">Verwenden Sie die gleiche API zum Konfigurieren anderer Kestrel-Einstellungen für bestimmte Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="788db-210">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

## <a name="bind-to-a-unix-socket"></a><span data-ttu-id="788db-211">Binden an einen Unix-Socket</span><span class="sxs-lookup"><span data-stu-id="788db-211">Bind to a Unix socket</span></span>

<span data-ttu-id="788db-212">Wie in diesem Beispiel dargestellt, lauschen Sie an einem Unix-Socket mit <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A>, um eine verbesserte Leistung mit Nginx zu erzielen:</span><span class="sxs-lookup"><span data-stu-id="788db-212">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="788db-213">Legen Sie den Eintrag `server` > `location` > `proxy_pass` in der Nginx-Konfigurationsdatei auf `http://unix:/tmp/{KESTREL SOCKET}:/;` fest.</span><span class="sxs-lookup"><span data-stu-id="788db-213">In the Nginx configuration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="788db-214">`{KESTREL SOCKET}` ist der Name des für <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> bereitgestellten Socket (zum Beispiel `kestrel-test.sock` im vorherigen Beispiel).</span><span class="sxs-lookup"><span data-stu-id="788db-214">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="788db-215">Stellen Sie sicher, dass der Socket von Nginx beschreibbar ist (z. B. `chmod go+w /tmp/kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="788db-215">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

## <a name="port-0"></a><span data-ttu-id="788db-216">Port 0</span><span class="sxs-lookup"><span data-stu-id="788db-216">Port 0</span></span>

<span data-ttu-id="788db-217">Wenn die Portnummer `0` angegeben wird, wird Kestrel dynamisch an einen verfügbaren Port gebunden.</span><span class="sxs-lookup"><span data-stu-id="788db-217">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="788db-218">Im folgenden Beispiel wird veranschaulicht, wie bestimmt werden kann, für welchen Port Kestrel zur Laufzeit eine Bindung erstellt hat:</span><span class="sxs-lookup"><span data-stu-id="788db-218">The following example shows how to determine which port Kestrel bound at runtime:</span></span>

[!code-csharp[](samples/5.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="788db-219">Wenn die App ausgeführt wird, gibt das Ausgabefenster der Konsole den dynamischen Port an, über den die App erreicht werden kann:</span><span class="sxs-lookup"><span data-stu-id="788db-219">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

## <a name="limitations"></a><span data-ttu-id="788db-220">Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="788db-220">Limitations</span></span>

<span data-ttu-id="788db-221">Konfigurieren Sie Endpunkte mithilfe der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="788db-221">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls%2A>
* <span data-ttu-id="788db-222">Befehlszeilenargument `--urls`</span><span class="sxs-lookup"><span data-stu-id="788db-222">`--urls` command-line argument</span></span>
* <span data-ttu-id="788db-223">Hostkonfigurationsschlüssel `urls`</span><span class="sxs-lookup"><span data-stu-id="788db-223">`urls` host configuration key</span></span>
* <span data-ttu-id="788db-224">Umgebungsvariable `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="788db-224">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="788db-225">Diese Methoden sind nützlich, wenn Ihr Code mit anderen Servern als Kestrel funktionieren soll.</span><span class="sxs-lookup"><span data-stu-id="788db-225">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="788db-226">Beachten Sie jedoch die folgenden Einschränkungen:</span><span class="sxs-lookup"><span data-stu-id="788db-226">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="788db-227">HTTPS kann nicht mit diesen Ansätzen verwendet werden, außer ein Standardzertifikat wird in der HTTPS-Endpunktkonfiguration angegeben (z. B. wenn Sie wie zuvor in diesem Artikel gezeigt die `KestrelServerOptions`-Konfiguration oder eine Konfigurationsdatei verwenden).</span><span class="sxs-lookup"><span data-stu-id="788db-227">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this article).</span></span>
* <span data-ttu-id="788db-228">Wenn die Ansätze `Listen` und `UseUrls` gleichzeitig verwendet werden, überschreiben die `Listen`-Endpunkte die `UseUrls`-Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="788db-228">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

## <a name="iis-endpoint-configuration"></a><span data-ttu-id="788db-229">IIS-Endpunktkonfiguration</span><span class="sxs-lookup"><span data-stu-id="788db-229">IIS endpoint configuration</span></span>

<span data-ttu-id="788db-230">Bei der Verwendung von IIS werden die URL-Bindungen für IIS-Überschreibungsbindungen durch `Listen` oder `UseUrls` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="788db-230">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="788db-231">Weitere Informationen finden Sie unter [ASP.NET Core Module (ASP.NET Core-Modul)](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="788db-231">For more information, see [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

## <a name="listenoptionsprotocols"></a><span data-ttu-id="788db-232">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="788db-232">ListenOptions.Protocols</span></span>

<span data-ttu-id="788db-233">Die `Protocols`-Eigenschaft richtet die HTTP-Protokolle (`HttpProtocols`) ein, die für einen Verbindungsendpunkt oder für den Server aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="788db-233">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="788db-234">Weisen Sie der `Protocols`-Eigenschaft einen Wert aus der `HttpProtocols`-Enumeration zu.</span><span class="sxs-lookup"><span data-stu-id="788db-234">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="788db-235">`HttpProtocols`-Enumerationswert</span><span class="sxs-lookup"><span data-stu-id="788db-235">`HttpProtocols` enum value</span></span> | <span data-ttu-id="788db-236">Zulässiges Verbindungsprotokoll</span><span class="sxs-lookup"><span data-stu-id="788db-236">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="788db-237">Nur HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="788db-237">HTTP/1.1 only.</span></span> <span data-ttu-id="788db-238">Kann mit oder ohne TLS verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="788db-238">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="788db-239">Nur HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="788db-239">HTTP/2 only.</span></span> <span data-ttu-id="788db-240">Kann nur ohne TLS verwendet werden, wenn der Client einen [Vorabkenntnis-Modus](https://tools.ietf.org/html/rfc7540#section-3.4) unterstützt.</span><span class="sxs-lookup"><span data-stu-id="788db-240">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="788db-241">HTTP/1.1 und HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="788db-241">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="788db-242">Für HTTP/2 muss der Client HTTP/2 im TLS [ALPN-Handshake (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) auswählen, andernfalls wird standardmäßig eine HTTP/1.1 verwendet.</span><span class="sxs-lookup"><span data-stu-id="788db-242">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="788db-243">Der Standardwert von `ListenOptions.Protocols` ist für alle Endpunkte `HttpProtocols.Http1AndHttp2`.</span><span class="sxs-lookup"><span data-stu-id="788db-243">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="788db-244">TLS-Einschränkungen für HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="788db-244">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="788db-245">TLS Version 1.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="788db-245">TLS version 1.2 or later</span></span>
* <span data-ttu-id="788db-246">Erneute Aushandlung deaktiviert</span><span class="sxs-lookup"><span data-stu-id="788db-246">Renegotiation disabled</span></span>
* <span data-ttu-id="788db-247">Komprimierung deaktiviert</span><span class="sxs-lookup"><span data-stu-id="788db-247">Compression disabled</span></span>
* <span data-ttu-id="788db-248">Minimale Größen für Austausch von flüchtigen Schlüsseln:</span><span class="sxs-lookup"><span data-stu-id="788db-248">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="788db-249">ECDHE (Elliptic Curve Diffie-Hellman) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: mindestens 224 Bits</span><span class="sxs-lookup"><span data-stu-id="788db-249">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="788db-250">DHE (Finite Field Diffie-Hellman) &lbrack;`TLS12`&rbrack;: mindestens 2048 Bits</span><span class="sxs-lookup"><span data-stu-id="788db-250">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="788db-251">Verschlüsselungssammlung nicht verboten</span><span class="sxs-lookup"><span data-stu-id="788db-251">Cipher suite not prohibited.</span></span> 

<span data-ttu-id="788db-252">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; mit der elliptischen P-256-Kurve &lbrack;`FIPS186`&rbrack; wird standardmäßig unterstützt.</span><span class="sxs-lookup"><span data-stu-id="788db-252">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="788db-253">Das folgende Beispiel erlaubt HTTP/1.1- und HTTP/2-Verbindungen an Port 8000.</span><span class="sxs-lookup"><span data-stu-id="788db-253">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="788db-254">Die Verbindungen werden durch TLS mit einem bereitgestellten Zertifikat geschützt:</span><span class="sxs-lookup"><span data-stu-id="788db-254">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="788db-255">Unter Linux kann <xref:System.Net.Security.CipherSuitesPolicy> zum Filtern von TLS-Handshakes auf Verbindungsbasis verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="788db-255">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

```csharp
// using System.Net.Security;
// using Microsoft.AspNetCore.Hosting;
// using Microsoft.AspNetCore.Server.Kestrel.Core;
// using Microsoft.Extensions.DependencyInjection;
// using Microsoft.Extensions.Hosting;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.OnAuthenticate = (context, sslOptions) =>
        {
            sslOptions.CipherSuitesPolicy = new CipherSuitesPolicy(
                new[]
                {
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,
                    // ...
                });
        };
    });
});
```

## <a name="connection-middleware"></a><span data-ttu-id="788db-256">Verbindungsmiddleware</span><span class="sxs-lookup"><span data-stu-id="788db-256">Connection Middleware</span></span>

<span data-ttu-id="788db-257">Benutzerdefinierte Verbindungsmiddleware kann bei Bedarf TLS-Handshakes auf Verbindungsbasis für bestimmte Verschlüsselungsverfahren filtern.</span><span class="sxs-lookup"><span data-stu-id="788db-257">Custom connection middleware can filter TLS handshakes on a per-connection basis for specific ciphers if necessary.</span></span>

<span data-ttu-id="788db-258">Im folgenden Beispiel wird <xref:System.NotSupportedException> für jeden Verschlüsselungsalgorithmus ausgelöst, der von der App nicht unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="788db-258">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="788db-259">Alternativ können Sie [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) definieren und mit einer Liste zulässiger Verschlüsselungssammlungen vergleichen.</span><span class="sxs-lookup"><span data-stu-id="788db-259">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="788db-260">Es wird keine Verschlüsselung mit einem [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType)-Verschlüsselungsalgorithmus verwendet.</span><span class="sxs-lookup"><span data-stu-id="788db-260">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

```csharp
// using System.Net;
// using Microsoft.AspNetCore.Connections;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.UseTlsFilter();
    });
});
```

```csharp
using System;
using System.Security.Authentication;
using Microsoft.AspNetCore.Connections.Features;

namespace Microsoft.AspNetCore.Connections
{
    public static class TlsFilterConnectionMiddlewareExtensions
    {
        public static IConnectionBuilder UseTlsFilter(
            this IConnectionBuilder builder)
        {
            return builder.Use((connection, next) =>
            {
                var tlsFeature = connection.Features.Get<ITlsHandshakeFeature>();

                if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
                {
                    throw new NotSupportedException("Prohibited cipher: " +
                        tlsFeature.CipherAlgorithm);
                }

                return next();
            });
        }
    }
}
```

<span data-ttu-id="788db-261">Die Verbindungsfilterung kann auch über einen <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder>-Lambdaausdruck konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="788db-261">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

```csharp
// using System;
// using System.Net;
// using System.Security.Authentication;
// using Microsoft.AspNetCore.Connections;
// using Microsoft.AspNetCore.Connections.Features;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.Use((context, next) =>
        {
            var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

            if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
            {
                throw new NotSupportedException(
                    $"Prohibited cipher: {tlsFeature.CipherAlgorithm}");
            }

            return next();
        });
    });
});
```

## <a name="set-the-http-protocol-from-configuration"></a><span data-ttu-id="788db-262">Festlegen des HTTP-Protokolls in der Konfiguration</span><span class="sxs-lookup"><span data-stu-id="788db-262">Set the HTTP protocol from configuration</span></span>

<span data-ttu-id="788db-263">`CreateDefaultBuilder` ruft `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` standardmäßig zum Laden der Kestrel-Konfiguration auf.</span><span class="sxs-lookup"><span data-stu-id="788db-263">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="788db-264">Das folgende *appsettings.json* -Beispiel richtet HTTP/1.1 als Standardverbindungsprotokoll für alle Endpunkte ein:</span><span class="sxs-lookup"><span data-stu-id="788db-264">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="788db-265">Das folgende *appsettings.json* -Beispiel richtet HTTP/1.1 als Verbindungsprotokoll für einen bestimmten Endpunkt ein:</span><span class="sxs-lookup"><span data-stu-id="788db-265">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1"
      }
    }
  }
}
```

<span data-ttu-id="788db-266">Protokolle, die in Code angegeben werden, setzen Werte außer Kraft, der durch die Konfiguration festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="788db-266">Protocols specified in code override values set by configuration.</span></span>

## <a name="url-prefixes"></a><span data-ttu-id="788db-267">URL-Präfixe</span><span class="sxs-lookup"><span data-stu-id="788db-267">URL prefixes</span></span>

<span data-ttu-id="788db-268">Bei der Verwendung von `UseUrls`, dem Befehlszeilenargument `--urls`, dem Hostkonfigurationsschlüssel `urls` oder der Umgebungsvariable `ASPNETCORE_URLS` können die URL-Präfixe in den folgenden Formaten vorliegen.</span><span class="sxs-lookup"><span data-stu-id="788db-268">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="788db-269">Nur HTTP-URL-Präfixe sind gültig.</span><span class="sxs-lookup"><span data-stu-id="788db-269">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="788db-270">Kestrel unterstützt HTTPS nicht beim Konfigurieren von URL-Bindungen mit `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="788db-270">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="788db-271">IPv4-Adresse mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="788db-271">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="788db-272">Bei `0.0.0.0` handelt es sich um einen Sonderfall, für den eine Bindung an alle IPv4-Adressen erfolgt.</span><span class="sxs-lookup"><span data-stu-id="788db-272">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="788db-273">IPv6-Adresse mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="788db-273">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="788db-274">`[::]` stellt das Äquivalent von IPv6 zu `0.0.0.0` für IPv4 dar.</span><span class="sxs-lookup"><span data-stu-id="788db-274">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="788db-275">Hostname mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="788db-275">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="788db-276">Hostnamen, `*` und `+` sind nicht spezifisch.</span><span class="sxs-lookup"><span data-stu-id="788db-276">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="788db-277">Alle Elemente, die nicht als gültige IP-Adresse oder `localhost` erkannt werden, werden an alle IPv4- und IPv6-IP-Adressen gebunden.</span><span class="sxs-lookup"><span data-stu-id="788db-277">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="788db-278">Verwenden Sie [HTTP.sys](xref:fundamentals/servers/httpsys) oder einen Reverseproxyserver zum Binden verschiedener Hostnamen an verschiedene ASP.NET Core-Apps auf demselben Port.</span><span class="sxs-lookup"><span data-stu-id="788db-278">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server.</span></span> <span data-ttu-id="788db-279">Beispiele für Reverseproxyserver sind IIS, Nginx oder Apache.</span><span class="sxs-lookup"><span data-stu-id="788db-279">Reverse proxy server examples include IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="788db-280">Das Hosting in einer Reverseproxykonfiguration erfordert [Hostfilterung](xref:fundamentals/servers/kestrel/host-filtering).</span><span class="sxs-lookup"><span data-stu-id="788db-280">Hosting in a reverse proxy configuration requires [host filtering](xref:fundamentals/servers/kestrel/host-filtering).</span></span>

* <span data-ttu-id="788db-281">`localhost`-Hostname mit Portnummer oder Loopback-IP mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="788db-281">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="788db-282">Wenn `localhost` angegeben ist, versucht Kestrel, eine Bindung zu IPv4- und IPv6-Loopback-Schnittstellen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="788db-282">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="788db-283">Wenn der erforderliche Port von einem anderen Dienst auf einer der Loopback-Schnittstellen verwendet wird, tritt beim Starten von Kestrel ein Fehler auf.</span><span class="sxs-lookup"><span data-stu-id="788db-283">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="788db-284">Wenn eine der Loopback-Schnittstellen aus anderen Gründen nicht verfügbar ist (meistens durch die fehlende Unterstützung von IPv6), protokolliert Kestrel eine Warnung.</span><span class="sxs-lookup"><span data-stu-id="788db-284">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>
