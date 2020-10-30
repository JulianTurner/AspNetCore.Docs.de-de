---
title: Client-IP-SafeList für ASP.net Core
author: damienbod
description: Erfahren Sie, wie Sie Middleware oder Aktionsfilter schreiben, um Remote-IP-Adressen anhand einer Liste genehmigter IP-Adressen zu überprüfen.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/12/2020
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
uid: security/ip-safelist
ms.openlocfilehash: dfc134b97bb0976bc682a53d536cd27785550c7d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059662"
---
# <a name="client-ip-safelist-for-aspnet-core"></a>Client-IP-SafeList für ASP.net Core

Von [Damien Bowder](https://twitter.com/damien_bod) und [Tom Dykstra](https://github.com/tdykstra)
 
In diesem Artikel werden drei Möglichkeiten beschrieben, wie Sie eine IP-Adresse Safelist (auch als Zulassungsliste bezeichnet) in einer ASP.net Core-App implementieren. In einer begleitenden Beispiel-App werden alle drei Ansätze veranschaulicht. Verwenden Sie Folgendes:

* Middleware zum Überprüfen der Remote-IP-Adresse für jede Anforderung.
* MVC-Aktionsfilter zum Überprüfen der Remote-IP-Adresse von Anforderungen für bestimmte Controller oder Aktionsmethoden.
* Razor Seiten Filter zum Überprüfen der Remote-IP-Adresse von Razor Seiten Anforderungen.

In jedem Fall wird eine Zeichenfolge mit genehmigten Client-IP-Adressen in einer APP-Einstellung gespeichert. Die Middleware oder der Filter:

* Analysiert die Zeichenfolge in ein Array. 
* Überprüft, ob die Remote-IP-Adresse im Array vorhanden ist.

Der Zugriff ist zulässig, wenn das Array die IP-Adresse enthält. Andernfalls wird ein HTTP 403-Statuscode "verboten" zurückgegeben.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="ip-address-safelist"></a>IP-Adresse SafeList

In der Beispiel-App lautet die IP-Adresse SafeList:

* Wird von der- `AdminSafeList` Eigenschaft in der *appsettings.json* Datei definiert.
* Eine durch Semikolons getrennte Zeichenfolge, die sowohl [IPv4 (Internet Protocol Version 4)](https://wikipedia.org/wiki/IPv4) -als auch IPv6-Adressen [(Internet Protocol Version 6)](https://wikipedia.org/wiki/IPv6) enthalten kann.

[!code-json[](ip-safelist/samples/3.x/ClientIpAspNetCore/appsettings.json?range=1-3&highlight=2)]

Im vorherigen Beispiel sind die IPv4-Adressen von `127.0.0.1` und `192.168.1.5` und die IPv6-Loopback Adresse `::1` (komprimiertes Format für `0:0:0:0:0:0:0:1` ) zulässig.

## <a name="middleware"></a>Middleware

Die `Startup.Configure` -Methode fügt der `AdminSafeListMiddleware` Anforderungs Pipeline der APP den Typ der benutzerdefinierten Middleware hinzu. Die SafeList wird mit dem .net Core-Konfigurations Anbieter abgerufen und als Konstruktorparameter übergeben.

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureAddMiddleware)]

Die Middleware analysiert die Zeichenfolge in ein Array und sucht im Array nach der Remote-IP-Adresse. Wenn die Remote-IP-Adresse nicht gefunden wird, gibt die Middleware HTTP 403 unzulässig zurück. Dieser Überprüfungs Vorgang wird für HTTP GET-Anforderungen umgangen.

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Middlewares/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a>Aktionsfilter

Wenn Sie die SafeList-gesteuerte Zugriffs Steuerung für bestimmte MVC-Controller oder Aktionsmethoden verwenden möchten, verwenden Sie einen Aktionsfilter. Zum Beispiel:

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckActionFilter.cs?name=snippet_ClassOnly)]

`Startup.ConfigureServices`Fügen Sie in den Aktionsfilter der MVC-Filter Auflistung hinzu. Im folgenden Beispiel `ClientIpCheckActionFilter` wird ein Aktionsfilter hinzugefügt. Eine SafeList und eine Konsolen Protokollierungs Instanz werden als Konstruktorparameter übergeben.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

Der Aktionsfilter kann dann mit dem [[Service Filter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) -Attribut auf einen Controller oder eine Aktionsmethode angewendet werden:

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_ActionFilter&highlight=1)]

In der Beispiel-APP wird der Aktionsfilter auf die Aktionsmethode des Controllers angewendet `Get` . Wenn Sie die APP testen, indem Sie Folgendes senden:

* Eine HTTP GET-Anforderung, das `[ServiceFilter]` -Attribut überprüft die Client-IP-Adresse. Wenn der Zugriff auf die `Get` Aktionsmethode zulässig ist, wird eine Variation der folgenden Konsolenausgabe durch den Aktionsfilter und die Aktionsmethode erzeugt:

    ```
    dbug: ClientIpSafelistComponents.Filters.ClientIpCheckActionFilter[0]
          Remote IpAddress: ::1
    dbug: ClientIpAspNetCore.Controllers.ValuesController[0]
          successful HTTP GET    
    ```

* Ein anderes HTTP-Anforderungs Verb als Get, die `AdminSafeListMiddleware` Middleware überprüft die Client-IP-Adresse.

## <a name="no-locrazor-pages-filter"></a>Razor Seiten Filter

Wenn Sie die SafeList-gesteuerte Zugriffs Steuerung für eine Razor pages-App verwenden möchten, verwenden Sie einen Razor Seiten Filter. Zum Beispiel:

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckPageFilter.cs?name=snippet_ClassOnly)]

`Startup.ConfigureServices`Aktivieren Sie in den Razor Seiten Filter, indem Sie ihn der MVC-Filter Auflistung hinzufügen. Im folgenden Beispiel `ClientIpCheckPageFilter` Razor wird ein Seiten Filter hinzugefügt. Eine SafeList und eine Konsolen Protokollierungs Instanz werden als Konstruktorparameter übergeben.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

Wenn die *Index* Seite der Beispiel-APP Razor angefordert wird, Razor überprüft der Seiten Filter die Client-IP-Adresse. Der Filter erzeugt eine Variation der folgenden Konsolenausgabe:

```
dbug: ClientIpSafelistComponents.Filters.ClientIpCheckPageFilter[0]
      Remote IpAddress: ::1
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/middleware/index>
* [Aktionsfilter](xref:mvc/controllers/filters#action-filters)
* <xref:razor-pages/filter>
