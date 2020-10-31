---
title: Übersicht über die ASP.NET Core-Sicherheit
author: rick-anderson
description: Erfahren Sie mehr über die Grundlagen der Authentifizierung, Autorisierung und Sicherheit in ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
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
uid: security/index
ms.openlocfilehash: 3c86c66bebe8a5ce1c195ebf931193e7e2a73fef
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051628"
---
# <a name="overview-of-aspnet-core-security"></a>Übersicht über die ASP.NET Core-Sicherheit

ASP.NET Core ermöglicht es Entwicklern, die Sicherheit ihrer Apps einfach zu konfigurieren und zu verwalten. ASP.NET Core umfasst Features für die Verwaltung von Authentifizierung, Autorisierung, Datenschutz, HTTPS-Erzwingung, App-Geheimnissen, Schutz vor XSRF/CSRF (Cross-Site-Request-Forgery, websiteübergreifende Anforderungsfälschung) und CORS-Verwaltung. Diese Sicherheitsfunktionen ermöglichen es Ihnen, robuste und dennoch sichere ASP.NET Core-Apps zu erstellen.

## <a name="aspnet-core-security-features"></a>ASP.NET Core-Sicherheitsfeatures

ASP.NET Core stellt zahlreiche Tools und Bibliotheken zur Verfügung, um Ihre Apps zu sichern (einschließlich integrierter Identitätsanbieter). Sie können jedoch auch Identitätsdienste von Drittanbietern wie Facebook, Twitter und LinkedIn nutzen. Mit ASP.NET Core können Sie ganz einfach App-Geheimnisse verwalten, die eine Möglichkeit darstellen, vertrauliche Informationen zu speichern und zu verwenden, ohne sie im Code offenlegen zu müssen.

## <a name="authentication-vs-authorization"></a>Authentifizierung im Vergleich zu Autorisierung

Authentifizierung ist ein Vorgang, bei dem ein Benutzer Anmeldeinformationen bereitstellt, die dann mit den Angaben verglichen werden, die in einem Betriebssystem, einer Datenbank, einer App oder einer Ressource gespeichert sind. Wenn diese übereinstimmen, authentifizieren sich die Benutzer erfolgreich und können dann während eines Autorisierungsprozesses Aktionen ausführen, für die sie berechtigt sind. Die Autorisierung bezieht sich auf den Prozess, der festlegt, welche Aktionen ein Benutzer ausführen darf.

Eine weitere Möglichkeit, Authentifizierung zu definieren, besteht darin, sie als eine Möglichkeit zu betrachten, einen „Raum“ (z.B. einen Server, eine Datenbank, eine App oder eine Ressource) zu betreten, während die Autorisierung darin besteht, welche Aktionen der Benutzer mit welchen Objekten innerhalb dieses „Raums“ (Server, Datenbank oder App) ausführen kann.

## <a name="common-vulnerabilities-in-software"></a>Häufige Sicherheitsrisiken in Software

ASP.NET Core und EF enthalten Features, die Ihnen helfen, Ihre Anwendungen zu schützen und Sicherheitsverletzungen zu verhindern. Die folgende Liste von Links verweist auf die Dokumentation, die Techniken zur Vermeidung der häufigsten Sicherheitsrisiken in Web-Apps beschreibt:

* [Angriffe durch Cross-Site-Scripting (XSS)](xref:security/cross-site-scripting)
* [Angriffe durch Einschleusung von SQL-Befehlen](/ef/core/querying/raw-sql)
* [Angriffe durch XSRF/CSRF (Cross-Site Request Forgery, websiteübergreifende Anforderungsfälschung)](xref:security/anti-request-forgery)
* [Offene Weiterleitungsangriffe](xref:security/preventing-open-redirects)

Es gibt weitere Sicherheitsrisiken, die Sie kennen sollten. Weitere Informationen finden Sie in den Artikeln im Abschnitt **Security and Identity** (Sicherheit und Identität) im Inhaltsverzeichnis.
