---
title: Out-of-Process-Hosting mit IIS und ASP.NET Core
author: rick-anderson
description: Hier erfahren Sie mehr über das Out-of-Process-Hosting mit IIS und das ASP.NET Core-Modul.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/out-of-process-hosting
ms.openlocfilehash: 048a18349de4d784ae4abb33bd86a2d9c08c609d
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2020
ms.locfileid: "91755165"
---
# <a name="out-of-process-hosting-with-iis-and-aspnet-core"></a>Out-of-Process-Hosting mit IIS und ASP.NET Core 

Da ASP.NET Core-Apps in einem Prozess getrennt vom IIS-Arbeitsprozess ausgeführt werden, führt das ASP.NET Core-Modul die Prozessverwaltung durch. Das Modul startet den Prozess für die ASP.NET Core-App, wenn die erste Anforderung eingeht und startet die App neu, wenn sie heruntergefahren wird oder abstürzt. Dies ist im Prinzip das gleiche Verhalten wie bei Apps, die prozessintern ausgeführt und durch den [Windows-Prozessaktivierungsdienst (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) verwaltet werden.

Das folgende Diagramm zeigt die Beziehung zwischen IIS, dem ASP.NET Core-Modul und einer Out-of-Process gehosteten App:

![Das ASP.NET Core-Modul im Out-of-Process-Hostingszenario](index/_static/ancm-outofprocess.png)

1. Anforderungen gehen aus dem Internet an den Treiber „HTTP.sys“ ein, der im Kernelmodus betrieben wird.
1. Der Treiber leitet die Anforderungen an IIS auf dem konfigurierten Port der Webseite weiter. Der konfigurierte Port ist normalerweise 80 (HTTP) oder 443 (HTTPS).
1. Das Modul leitet die Anforderung an Kestrel auf einem zufälligen Port der App weiter. Der zufällige Port entspricht nicht Port 80 oder 443.

<!-- make this a bullet list -->
Das ASP.NET Core-Modul gibt den Port beim Start über eine Umgebungsvariable an. Mit der <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A>-Erweiterung wird der Server so konfiguriert, dass er an `http://localhost:{PORT}` lauscht. Zusätzliche Überprüfungen werden durchgeführt. Anforderungen, die nicht vom Modul stammen, werden abgelehnt. Das Modul unterstützt die HTTPS-Weiterleitung nicht. Deshalb werden Anforderungen über HTTP weitergeleitet, selbst wenn sie von IIS über HTTPS empfangen wurden.

Nachdem Kestrel die Anforderung vom Modul erhalten hat, wird die Anforderung an die ASP.NET Core-Middlewarepipeline weitergeleitet. Die Middleware-Pipeline behandelt die Anforderung und gibt sie als `HttpContext`-Instanz an die App-Logik weiter. Die durch IIS-Integration hinzugefügte Middleware aktualisiert das Schema, die Remote-IP und die Pfadbasis, um der Weiterleitung der Anforderung an Kestrel Rechnung zu tragen. Die Antwort der App wird dann an IIS zurückgegeben und an den HTTP-Client weitergeleitet, der die Anforderung initiiert hat.

Einen Konfigurationsleitfaden für das ASP.NET Core-Modul finden Sie unter <xref:host-and-deploy/aspnet-core-module>.

Weitere Informationen zum Hosten finden Sie unter [Hosten in ASP.NET Core](xref:fundamentals/index#host).

## <a name="application-configuration"></a>Anwendungskonfiguration

### <a name="enable-the-iisintegration-components"></a>Aktivieren der IISIntegration-Komponenten

Rufen Sie beim Erstellen eines Hosts in `CreateHostBuilder` (`Program.cs`) <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> ab, um die IIS-Integration zu aktivieren:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

Weitere Informationen zu `CreateDefaultBuilder` finden Sie unter <xref:fundamentals/host/generic-host#default-builder-settings>.


**Out-of-Process-Hostingmodell**

Schließen Sie zur Konfiguration von IIS-Optionen eine Dienstkonfiguration für <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A> ein. Im folgenden Beispiel wird Verhindert, dass die App `HttpContext.Connection.ClientCertificate` auffüllt:

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| Option                         | Standard | Einstellung |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Bei Festlegung auf `true` legt die [Middleware für die IIS-Integration](#enable-the-iisintegration-components) den per [Windows-Authentifizierung](xref:security/authentication/windowsauth) authentifizierten `HttpContext.User` fest. Bei Festlegung auf `false` stellt die Middleware nur eine Identität für `HttpContext.User` bereit und antwortet auf explizite Anforderungen durch `AuthenticationScheme`. Die Windows-Authentifizierung muss in IIS aktiviert sein, damit `AutomaticAuthentication` funktioniert. Weitere Informationen finden Sie im Thema [Windows-Authentifizierung](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName`    | `null`  | Legt den Anzeigename fest, der Benutzern auf Anmeldungsseiten angezeigt wird |
| `ForwardClientCertificate`     | `true`  | Wenn diese Option `true` ist und der Anforderungsheader `MS-ASPNETCORE-CLIENTCERT` vorhanden ist, wird das `HttpContext.Connection.ClientCertificate` aufgefüllt. |


### <a name="proxy-server-and-load-balancer-scenarios"></a>Proxyserver und Lastenausgleichsszenarien

Die [Middleware für die IIS-Integration](#enable-the-iisintegration-components) und das ASP.NET Core-Modul sind zur Weiterleitung des

* Schemas (HTTP/HTTPS)
* und der Remote-IP-Adresse konfiguriert, von der die Anforderung stammt.

Die [Middleware für die IIS-Integration](#enable-the-iisintegration-components) konfiguriert die Middleware für weitergeleitete Header.

Möglicherweise ist zusätzliche Konfiguration für Apps erforderlich, die hinter weiteren Proxyservern und Lastenausgleichsmodulen (Load Balancer) gehostet werden. Weitere Informationen hierzu feinden Sie unter [Konfigurieren von ASP.NET Core zur Verwendung mit Proxyservern und Lastenausgleich](xref:host-and-deploy/proxy-load-balancer).


### <a name="out-of-process-hosting-model"></a>Out-of-Process-Hostingmodell

Um eine App für Out-of-Process-Hosting zu konfigurieren, legen Sie den Wert der `<AspNetCoreHostingModel>`-Eigenschaft in der Projektdatei ( `.csproj`) auf `OutOfProcess` fest:

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

Das In-Process-Hosting wird mithilfe von `InProcess` (Standardwert) festgelegt.

Beim Wert von `<AspNetCoreHostingModel>` wird die Groß-/Kleinschreibung nicht beachtet, sodass `inprocess` und `outofprocess` gültige Werte sind.

Der [Kestrel](xref:fundamentals/servers/kestrel)-Server wird anstelle des IIS-HTTP-Servers (`IISHttpServer`) verwendet.

Bei Out-of-Process ruft [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> auf, um Folgendes zu tun:

* Konfigurieren des Ports und des Basispfads, den der Server überwachen soll, wenn er hinter dem ASP.NET Core-Modul ausgeführt wird.
* Konfigurieren des Hosts zum Erfassen von Startfehlern.

### <a name="process-name"></a>Prozessname

`Process.GetCurrentProcess().ProcessName` meldet `w3wp`/`iisexpress` (In-Process) oder `dotnet` (Out-of-Process).

Viele native Module, z.B. die Windows-Authentifizierung, bleiben aktiv. Weitere Informationen zu IIS-Modulen, die im ASP.NET Core-Modul aktiv sind, finden Sie unter <xref:host-and-deploy/iis/modules>.

Das ASP.NET Core-Modul kann außerdem:

* Umgebungsvariablen für den Arbeitsprozess festlegen
* Die stdout-Ausgabe im Dateispeicher protokollieren, um Probleme beim Start zu beheben
* Windows-Authentifizierungstoken weiterleiten
