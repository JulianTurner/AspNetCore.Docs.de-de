---
title: In-Process-Hosting mit IIS und ASP.NET Core
author: rick-anderson
description: Hier erfahren Sie mehr über das In-Process-Hosting mit IIS und dem ASP.NET Core-Modul.
monikerRange: '>= aspnetcore-5.0'
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
uid: host-and-deploy/iis/in-process-hosting
ms.openlocfilehash: 47dc6f65f398ecce45c563c359dfde6e17d1dc1b
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058479"
---
# <a name="in-process-hosting-with-iis-and-aspnet-core"></a>In-Process-Hosting mit IIS und ASP.NET Core 

Beim Einsatz von In-Process-Hosting wird eine ASP.NET Core-App im gleichen Prozess wie ihr IIS-Workerprozess ausgeführt. Durch das In-Process-Hosting wird die Leistung des Out-of-Process-Hosting verbessert, da Anforderungen nicht per Proxy über den Loopbackadapter weitergeleitet werden. Dabei handelt es sich um eine Netzwerkschnittstelle, die ausgehenden Netzwerkdatenverkehr zum selben Computer zurück leitet.

Das folgende Diagramm zeigt die Beziehung zwischen IIS, dem ASP.NET Core-Modul und einer In-Process gehosteten App:

![Das ASP.NET Core-Modul im In-Process-Hostingszenario](index/_static/ancm-inprocess.png)

## <a name="enable-in-process-hosting"></a>Aktivieren von In-Process-Hosting

Seit ASP.NET Core 3.0 ist In-Process-Hosting standardmäßig für alle für die IIS bereitgestellten Apps aktiviert.

Legen Sie den Wert der `<AspNetCoreHostingModel>`-Eigenschaft in der Projektdatei (`.csproj`) auf `InProcess` fest, um eine App explizit für In-Process-Hosting zu konfigurieren:

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

## <a name="general-architecture"></a>Allgemeine Architektur

Der allgemeine Anforderungsflow lautet wie folgt:

1. Eine Anforderung geht aus dem Web beim HTTP.sys-Treiber im Kernelmodus ein.
1. Der Treiber leitet die native Anforderung an IIS auf dem konfigurierten Port der Webseite weiter, normalerweise 80 (HTTP) oder 443 (HTTPS).
1. Das ASP.NET Core-Modul empfängt die native Anforderung und übergibt sie an den IIS-HTTP-Server (`IISHttpServer`). Der IIS-HTTP-Server ist eine prozessinterne Serverimplementierung für IIS, die die Anforderung vom nativen Modus in den verwalteten Modus konvertiert.

Nach der Verarbeitung der Anforderung durch den IIS-HTTP-Server:

1. Die Anforderung wird an die ASP.NET Core-Middlewarepipeline gesendet.
1. Die Middleware-Pipeline behandelt die Anforderung und gibt sie als `HttpContext`-Instanz an die App-Logik weiter.
1. Die Antwort der App wird über den IIS-HTTP-Server zurück an IIS übergeben.
1. IIS übermittelt die Antwort an den Client, der die Anforderung initiiert hat.

`CreateDefaultBuilder` fügt eine <xref:Microsoft.AspNetCore.Hosting.Server.IServer>-Instanz hinzu, indem die <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A>-Methode aufgerufen wird, um die [CoreCLR](/dotnet/standard/glossary#coreclr) zu starten und die App im IIS-Workerprozess zu hosten (`w3wp.exe` oder `iisexpress.exe`). Leistungstests weisen darauf hin, dass das In-Process-Hosting einer .NET Core-App einen weitaus höheren Anforderungsdurchsatz im Vergleich zum Out-of-Process-Hosting der App mit Weiterleitung der Anforderungen über einen Proxy an [Kestrel](xref:fundamentals/servers/kestrel) bietet.

Apps, die als einzelne ausführbare Datei veröffentlicht wurden, können vom In-Prozess-Hostingmodell nicht geladen werden.

## <a name="application-configuration"></a>Anwendungskonfiguration

Schließen Sie zur Konfiguration von IIS-Optionen eine Dienstkonfiguration für <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A> ein. Mit dem folgenden Beispiel wird AutomaticAuthentication deaktiviert:

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| Option | Standard | Einstellung |
| ------ | :-----: | ------- |
| `AutomaticAuthentication` | `true` | Bei Festlegung auf `true` legt der Server den per [Windows-Authentifizierung](xref:security/authentication/windowsauth) authentifizierten `HttpContext.User` fest. Bei Festlegung auf `false` stellt der Server nur eine Identität für `HttpContext.User` bereit und antwortet auf explizite Anforderungen durch `AuthenticationScheme`. Die Windows-Authentifizierung muss in IIS aktiviert sein, damit `AutomaticAuthentication` funktioniert. Weitere Informationen finden Sie unter [Windows-Authentifizierung](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName` | `null` | Legt den Anzeigename fest, der Benutzern auf Anmeldungsseiten angezeigt wird |
| `AllowSynchronousIO` | `false` | Steuert, ob synchrone E/A-Vorgänge für `HttpContext.Request` und `HttpContext.Response` zulässig sind. |
| `MaxRequestBodySize` | `30000000` | Dient zum Abrufen oder Festlegen der maximalen Größe des Anforderungstexts für `HttpRequest`. Beachten Sie, dass IIS selbst das Limit `maxAllowedContentLength` aufweist, das vor der in den `IISServerOptions` festgelegten `MaxRequestBodySize` verarbeitet wird. Das Ändern von `MaxRequestBodySize` hat keine Auswirkungen auf `maxAllowedContentLength`. Zum Erhöhen von `maxAllowedContentLength` fügen Sie einen Eintrag in `web.config` hinzu, um `maxAllowedContentLength` auf einen höheren Wert festzulegen. Weitere Informationen hierzu finden Sie unter [Konfiguration](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration). |

## <a name="differences-between-in-process-and-out-of-process-hosting"></a>Unterschiede zwischen In-Process- und Out-of-Process-Hosting

Die folgenden Merkmale treffen für In-Process-Hosting zu:

* Der IIS-HTTP-Server (`IISHttpServer`) wird anstelle des [Kestrel](xref:fundamentals/servers/kestrel)-Servers verwendet. Beim In-Process-Hosting ruft [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> zu folgendem Zweck auf:

  * Registrieren des `IISHttpServer`.
  * Konfigurieren des Ports und des Basispfads, den der Server überwachen soll, wenn er hinter dem ASP.NET Core-Modul ausgeführt wird.
  * Konfigurieren des Hosts zum Erfassen von Startfehlern.

* Das [`requestTimeout`-Attribut](xref:host-and-deploy/iis/web-config#attributes-of-the-aspnetcore-element) trifft auf das In-Process-Hosting nicht zu.

* Das gemeinsame Verwenden eines Anwendungspools durch mehrere Apps wird nicht unterstützt. Verwenden Sie einen Anwendungspool pro App.

* Die Architektur (Bitbreite) der App und der installierten Runtime (x64 oder x86) müssen mit der Architektur des Anwendungspools übereinstimmen. Für Apps, die für 32 Bit (x86) veröffentlicht wurden, müssen z. B. 32 Bit für ihre IIS-Anwendungspools aktiviert sein. Weitere Informationen finden Sie im Abschnitt [Erstellen der IIS-Website](xref:tutorials/publish-to-iis#create-the-iis-site).

* Verbindungstrennungen von Clients werden erkannt. Das [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted%2A)-Abbruchtoken wird abgebrochen, wenn der Client die Verbindung trennt.

* Beim In-Process-Hosting wird <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync%2A> nicht intern aufgerufen, um einen Benutzer zu initialisieren. Deshalb ist eine <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>-Implementierung, die verwendet wird, um Ansprüche nach jeder Authentifizierung zu transformieren, nicht standardmäßig aktiviert. Rufen Sie <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A> auf, um Authentifizierungsdienste hinzuzufügen, wenn Ansprüche mit einer <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>-Implementierung transformiert werden:

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```
  
* [Bereitstellungen von Webpaketen (Einzeldateien)](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) werden nicht unterstützt.
