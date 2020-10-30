---
title: Richtlinien Schemas in ASP.net Core
author: rick-anderson
description: Authentifizierungs Richtlinien Schemas vereinfachen die Erstellung eines einzelnen logischen Authentifizierungs Schemas.
ms.author: riande
ms.date: 12/05/2019
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
uid: security/authentication/policyschemes
ms.openlocfilehash: 63d931c926c9660f5d68d5a2ce292bf57efdb49c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053227"
---
# <a name="policy-schemes-in-aspnet-core"></a><span data-ttu-id="9254e-103">Richtlinien Schemas in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="9254e-103">Policy schemes in ASP.NET Core</span></span>

<span data-ttu-id="9254e-104">Authentifizierungs Richtlinien Schemas vereinfachen die Verwendung eines einzelnen logischen Authentifizierungs Schemas, das möglicherweise mehrere Ansätze verwendet.</span><span class="sxs-lookup"><span data-stu-id="9254e-104">Authentication policy schemes make it easier to have a single logical authentication scheme potentially use multiple approaches.</span></span> <span data-ttu-id="9254e-105">Beispielsweise kann ein Richtlinien Schema die Google-Authentifizierung für Herausforderungen und die :::no-loc(cookie)::: Authentifizierung für alles andere verwenden.</span><span class="sxs-lookup"><span data-stu-id="9254e-105">For example, a policy scheme might use Google authentication for challenges, and :::no-loc(cookie)::: authentication for everything else.</span></span> <span data-ttu-id="9254e-106">Die Authentifizierungs Richtlinien Schemas machen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="9254e-106">Authentication policy schemes make it:</span></span>

* <span data-ttu-id="9254e-107">Einfache Weiterleiten beliebiger Authentifizierungs Aktionen an ein anderes Schema.</span><span class="sxs-lookup"><span data-stu-id="9254e-107">Easy to forward any authentication action to another scheme.</span></span>
* <span data-ttu-id="9254e-108">Basierend auf der Anforderung dynamisch weiterleiten.</span><span class="sxs-lookup"><span data-stu-id="9254e-108">Forward dynamically based on the request.</span></span>

<span data-ttu-id="9254e-109">Alle Authentifizierungs Schemas, die abgeleitete <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> und den zugehörigen [authenticationhandler \<TOptions> ](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1)verwenden:</span><span class="sxs-lookup"><span data-stu-id="9254e-109">All authentication schemes that use derived <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> and the associated [AuthenticationHandler\<TOptions>](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):</span></span>

* <span data-ttu-id="9254e-110">Sind automatisch Richtlinien Schemas in ASP.net Core 2,1 und höher.</span><span class="sxs-lookup"><span data-stu-id="9254e-110">Are automatically policy schemes in ASP.NET Core 2.1 and later.</span></span>
* <span data-ttu-id="9254e-111">Kann durch Konfigurieren der Schema Optionen aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="9254e-111">Can be enabled via configuring the scheme's options.</span></span>

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a><span data-ttu-id="9254e-112">Beispiele</span><span class="sxs-lookup"><span data-stu-id="9254e-112">Examples</span></span>

<span data-ttu-id="9254e-113">Das folgende Beispiel zeigt ein Schema höherer Ebene, das Schemata auf niedrigerer Ebene kombiniert.</span><span class="sxs-lookup"><span data-stu-id="9254e-113">The following example shows a higher level scheme that combines lower level schemes.</span></span> <span data-ttu-id="9254e-114">Die Google-Authentifizierung wird für Herausforderungen verwendet, und die :::no-loc(cookie)::: Authentifizierung wird für alles andere verwendet:</span><span class="sxs-lookup"><span data-stu-id="9254e-114">Google authentication is used for challenges, and :::no-loc(cookie)::: authentication is used for everything else:</span></span>

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

<span data-ttu-id="9254e-115">Im folgenden Beispiel wird die dynamische Auswahl von Schemas pro Anforderung aktiviert.</span><span class="sxs-lookup"><span data-stu-id="9254e-115">The following example enables dynamic selection of schemes on a per request basis.</span></span> <span data-ttu-id="9254e-116">Das heißt, wie :::no-loc(cookie)::: s und API-Authentifizierung gemischt werden:</span><span class="sxs-lookup"><span data-stu-id="9254e-116">That is, how to mix :::no-loc(cookie):::s and API authentication:</span></span>

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]
