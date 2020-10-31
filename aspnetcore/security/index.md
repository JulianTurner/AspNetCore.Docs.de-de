---
title: Übersicht über die ASP.NET Core-Sicherheit
author: rick-anderson
description: Erfahren Sie mehr über die Grundlagen der Authentifizierung, Autorisierung und Sicherheit in ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/index
ms.openlocfilehash: 3c86c66bebe8a5ce1c195ebf931193e7e2a73fef
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051628"
---
# <a name="overview-of-aspnet-core-security"></a><span data-ttu-id="af8a8-103">Übersicht über die ASP.NET Core-Sicherheit</span><span class="sxs-lookup"><span data-stu-id="af8a8-103">Overview of ASP.NET Core Security</span></span>

<span data-ttu-id="af8a8-104">ASP.NET Core ermöglicht es Entwicklern, die Sicherheit ihrer Apps einfach zu konfigurieren und zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="af8a8-104">ASP.NET Core enables developers to easily configure and manage security for their apps.</span></span> <span data-ttu-id="af8a8-105">ASP.NET Core umfasst Features für die Verwaltung von Authentifizierung, Autorisierung, Datenschutz, HTTPS-Erzwingung, App-Geheimnissen, Schutz vor XSRF/CSRF (Cross-Site-Request-Forgery, websiteübergreifende Anforderungsfälschung) und CORS-Verwaltung.</span><span class="sxs-lookup"><span data-stu-id="af8a8-105">ASP.NET Core contains features for managing authentication, authorization, data protection, HTTPS enforcement, app secrets, XSRF/CSRF prevention, and CORS management.</span></span> <span data-ttu-id="af8a8-106">Diese Sicherheitsfunktionen ermöglichen es Ihnen, robuste und dennoch sichere ASP.NET Core-Apps zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="af8a8-106">These security features allow you to build robust yet secure ASP.NET Core apps.</span></span>

## <a name="aspnet-core-security-features"></a><span data-ttu-id="af8a8-107">ASP.NET Core-Sicherheitsfeatures</span><span class="sxs-lookup"><span data-stu-id="af8a8-107">ASP.NET Core security features</span></span>

<span data-ttu-id="af8a8-108">ASP.NET Core stellt zahlreiche Tools und Bibliotheken zur Verfügung, um Ihre Apps zu sichern (einschließlich integrierter Identitätsanbieter). Sie können jedoch auch Identitätsdienste von Drittanbietern wie Facebook, Twitter und LinkedIn nutzen.</span><span class="sxs-lookup"><span data-stu-id="af8a8-108">ASP.NET Core provides many tools and libraries to secure your apps including built-in identity providers, but you can use third-party identity services such as Facebook, Twitter, and LinkedIn.</span></span> <span data-ttu-id="af8a8-109">Mit ASP.NET Core können Sie ganz einfach App-Geheimnisse verwalten, die eine Möglichkeit darstellen, vertrauliche Informationen zu speichern und zu verwenden, ohne sie im Code offenlegen zu müssen.</span><span class="sxs-lookup"><span data-stu-id="af8a8-109">With ASP.NET Core, you can easily manage app secrets, which are a way to store and use confidential information without having to expose it in the code.</span></span>

## <a name="authentication-vs-authorization"></a><span data-ttu-id="af8a8-110">Authentifizierung im Vergleich zu Autorisierung</span><span class="sxs-lookup"><span data-stu-id="af8a8-110">Authentication vs. Authorization</span></span>

<span data-ttu-id="af8a8-111">Authentifizierung ist ein Vorgang, bei dem ein Benutzer Anmeldeinformationen bereitstellt, die dann mit den Angaben verglichen werden, die in einem Betriebssystem, einer Datenbank, einer App oder einer Ressource gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="af8a8-111">Authentication is a process in which a user provides credentials that are then compared to those stored in an operating system, database, app or resource.</span></span> <span data-ttu-id="af8a8-112">Wenn diese übereinstimmen, authentifizieren sich die Benutzer erfolgreich und können dann während eines Autorisierungsprozesses Aktionen ausführen, für die sie berechtigt sind.</span><span class="sxs-lookup"><span data-stu-id="af8a8-112">If they match, users authenticate successfully, and can then perform actions that they're authorized for, during an authorization process.</span></span> <span data-ttu-id="af8a8-113">Die Autorisierung bezieht sich auf den Prozess, der festlegt, welche Aktionen ein Benutzer ausführen darf.</span><span class="sxs-lookup"><span data-stu-id="af8a8-113">The authorization refers to the process that determines what a user is allowed to do.</span></span>

<span data-ttu-id="af8a8-114">Eine weitere Möglichkeit, Authentifizierung zu definieren, besteht darin, sie als eine Möglichkeit zu betrachten, einen „Raum“ (z.B. einen Server, eine Datenbank, eine App oder eine Ressource) zu betreten, während die Autorisierung darin besteht, welche Aktionen der Benutzer mit welchen Objekten innerhalb dieses „Raums“ (Server, Datenbank oder App) ausführen kann.</span><span class="sxs-lookup"><span data-stu-id="af8a8-114">Another way to think of authentication is to consider it as a way to enter a space, such as a server, database, app or resource, while authorization is which actions the user can perform to which objects inside that space (server, database, or app).</span></span>

## <a name="common-vulnerabilities-in-software"></a><span data-ttu-id="af8a8-115">Häufige Sicherheitsrisiken in Software</span><span class="sxs-lookup"><span data-stu-id="af8a8-115">Common Vulnerabilities in software</span></span>

<span data-ttu-id="af8a8-116">ASP.NET Core und EF enthalten Features, die Ihnen helfen, Ihre Anwendungen zu schützen und Sicherheitsverletzungen zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="af8a8-116">ASP.NET Core and EF contain features that help you secure your apps and prevent security breaches.</span></span> <span data-ttu-id="af8a8-117">Die folgende Liste von Links verweist auf die Dokumentation, die Techniken zur Vermeidung der häufigsten Sicherheitsrisiken in Web-Apps beschreibt:</span><span class="sxs-lookup"><span data-stu-id="af8a8-117">The following list of links takes you to documentation detailing techniques to avoid the most common security vulnerabilities in web apps:</span></span>

* [<span data-ttu-id="af8a8-118">Angriffe durch Cross-Site-Scripting (XSS)</span><span class="sxs-lookup"><span data-stu-id="af8a8-118">Cross-Site Scripting (XSS) attacks</span></span>](xref:security/cross-site-scripting)
* [<span data-ttu-id="af8a8-119">Angriffe durch Einschleusung von SQL-Befehlen</span><span class="sxs-lookup"><span data-stu-id="af8a8-119">SQL injection attacks</span></span>](/ef/core/querying/raw-sql)
* [<span data-ttu-id="af8a8-120">Angriffe durch XSRF/CSRF (Cross-Site Request Forgery, websiteübergreifende Anforderungsfälschung)</span><span class="sxs-lookup"><span data-stu-id="af8a8-120">Cross-Site Request Forgery (XSRF/CSRF) attacks</span></span>](xref:security/anti-request-forgery)
* [<span data-ttu-id="af8a8-121">Offene Weiterleitungsangriffe</span><span class="sxs-lookup"><span data-stu-id="af8a8-121">Open redirect attacks</span></span>](xref:security/preventing-open-redirects)

<span data-ttu-id="af8a8-122">Es gibt weitere Sicherheitsrisiken, die Sie kennen sollten.</span><span class="sxs-lookup"><span data-stu-id="af8a8-122">There are more vulnerabilities that you should be aware of.</span></span> <span data-ttu-id="af8a8-123">Weitere Informationen finden Sie in den Artikeln im Abschnitt **Security and :::no-loc(Identity):::** (Sicherheit und Identität) im Inhaltsverzeichnis.</span><span class="sxs-lookup"><span data-stu-id="af8a8-123">For more information, see the other articles in the **Security and :::no-loc(Identity):::** section of the table of contents.</span></span>
