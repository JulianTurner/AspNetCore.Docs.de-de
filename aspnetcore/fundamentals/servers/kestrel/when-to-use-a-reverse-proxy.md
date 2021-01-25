---
title: Anwendungsfälle für einen Reverseproxy mit dem Kestrel-Webserver für ASP.NET Core
author: rick-anderson
description: Hier erhalten Sie Informationen dazu, wann Sie einen Reverseproxy vor Kestrel verwenden sollten. Bei Kestrel handelt es sich um den plattformübergreifenden Webserver für ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/14/2021
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
uid: fundamentals/servers/kestrel/when-to-use-a-reverse-proxy
ms.openlocfilehash: fc89a9f841403bbccedff0a9c0720a08c11abdd6
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253839"
---
# <a name="when-to-use-kestrel-with-a-reverse-proxy"></a>Verwenden von Kestrel mit einem Reverseproxy

Kestrel kann eigenständig oder mit einem *Reverseproxyserver* wie z.B. [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) oder [Apache](https://httpd.apache.org/) verwendet werden. Ein Reverseproxyserver empfängt HTTP-Anforderungen aus dem Netzwerk und leitet diese an Kestrel weiter.

Kestrel bei Verwendung als Webserver mit direkter Internetverbindung:

![Kestrel kommuniziert direkt und ohne Reverseproxyserver mit dem Internet](_static/kestrel-to-internet2.png)

Kestrel bei Verwendung in einer Reverseproxykonfiguration:

![Kestrel kommuniziert indirekt mit dem Internet über einen Reverseproxyserver wie IIS, Nginx oder Apache](_static/kestrel-to-internet.png)

Jede der beiden Konfigurationen – mit oder ohne einen Reverseproxyserver – stellt eine unterstützte Hostingkonfiguration dar.

Wenn Kestrel als Edgeserver ohne Reverseproxyserver verwendet wird, wird das gemeinsame Nutzen derselben IP-Adresse und desselben Ports für mehrere Prozesse nicht unterstützt. Wenn Kestrel für das Lauschen an einem Port konfiguriert ist, verarbeitet Kestrel den gesamten Datenverkehr für diesen Port unabhängig von den `Host`-Headern der Anforderungen. Ein Reverseproxy, der Ports freigeben kann, kann Anforderungen an Kestrel über eine eindeutige IP und einen eindeutigen Port weiterleiten.

Auch wenn kein Reverseproxyserver erforderlich ist, kann die Verwendung eines solchen empfehlenswert sein.

Für einen Reverseproxy gilt Folgendes:

* Er kann die verfügbar gemachten öffentlichen Oberflächen der von ihm gehosteten Apps einschränken.
* Er stellt eine zusätzliche Ebene für Konfiguration und Schutz bereit.
* Er lässt sich besser in die vorhandene Infrastruktur integrieren.
* Er vereinfacht die Konfiguration von Lastenausgleich und sicherer Kommunikation (HTTPS). Nur der Reverseproxyserver erfordert ein X.509-Zertifikat, und dieser Server kann mit den Servern der App im internen Netzwerk über einfaches HTTP kommunizieren.

> [!WARNING]
> Das Hosting in einer Reverseproxykonfiguration erfordert [Hostfilterung](xref:fundamentals/servers/kestrel/host-filtering).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

<xref:host-and-deploy/proxy-load-balancer>

