---
title: Richtlinien Schemas in ASP.net Core
author: rick-anderson
description: Authentifizierungs Richtlinien Schemas vereinfachen die Erstellung eines einzelnen logischen Authentifizierungs Schemas.
ms.author: riande
ms.date: 12/05/2019
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
uid: security/authentication/policyschemes
ms.openlocfilehash: 63d931c926c9660f5d68d5a2ce292bf57efdb49c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053227"
---
# <a name="policy-schemes-in-aspnet-core"></a>Richtlinien Schemas in ASP.net Core

Authentifizierungs Richtlinien Schemas vereinfachen die Verwendung eines einzelnen logischen Authentifizierungs Schemas, das möglicherweise mehrere Ansätze verwendet. Beispielsweise kann ein Richtlinien Schema die Google-Authentifizierung für Herausforderungen und die cookie Authentifizierung für alles andere verwenden. Die Authentifizierungs Richtlinien Schemas machen Folgendes:

* Einfache Weiterleiten beliebiger Authentifizierungs Aktionen an ein anderes Schema.
* Basierend auf der Anforderung dynamisch weiterleiten.

Alle Authentifizierungs Schemas, die abgeleitete <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> und den zugehörigen [authenticationhandler \<TOptions> ](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1)verwenden:

* Sind automatisch Richtlinien Schemas in ASP.net Core 2,1 und höher.
* Kann durch Konfigurieren der Schema Optionen aktiviert werden.

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a>Beispiele

Das folgende Beispiel zeigt ein Schema höherer Ebene, das Schemata auf niedrigerer Ebene kombiniert. Die Google-Authentifizierung wird für Herausforderungen verwendet, und die cookie Authentifizierung wird für alles andere verwendet:

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

Im folgenden Beispiel wird die dynamische Auswahl von Schemas pro Anforderung aktiviert. Das heißt, wie cookie s und API-Authentifizierung gemischt werden:

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]
