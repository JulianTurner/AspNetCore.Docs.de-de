---
title: Migrieren von "ClaimsPrincipal. Current"
author: mjrousos
description: Erfahren Sie, wie Sie von ClaimsPrincipal. Current migrieren, um die Identität und Ansprüche des aktuellen authentifizierten Benutzers in ASP.net Core abzurufen.
ms.author: scaddie
ms.custom: mvc
ms.date: 03/26/2019
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
uid: migration/claimsprincipal-current
ms.openlocfilehash: 3aa0adb299789efbb071cdb934d43832a84cf540
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059766"
---
# <a name="migrate-from-claimsprincipalcurrent"></a>Migrieren von "ClaimsPrincipal. Current"

In ASP.NET 4. x-Projekten war es üblich, " [ClaimsPrincipal. Current](/dotnet/api/system.security.claims.claimsprincipal.current) " zum Abrufen der Identität und Ansprüche des aktuellen authentifizierten Benutzers zu verwenden. In ASP.net Core ist diese Eigenschaft nicht mehr festgelegt. Code, der von ihm abhängig war, muss aktualisiert werden, um die Identität des aktuellen authentifizierten Benutzers auf andere Weise zu erhalten.

## <a name="context-specific-state-instead-of-static-state"></a>Kontext spezifischer Zustand anstelle von statischem Zustand

Wenn Sie ASP.net Core verwenden, werden die Werte von `ClaimsPrincipal.Current` und `Thread.CurrentPrincipal` nicht festgelegt. Diese Eigenschaften stellen beide den statischen Zustand dar, der ASP.net Core im allgemeinen vermeidet. Stattdessen verwendet ASP.net Core [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (di), um Abhängigkeiten bereitzustellen, wie z. b. die Identität des aktuellen Benutzers. Die Identität des aktuellen Benutzers von di zu erhalten, ist auch besser testbar, da Test Identitäten problemlos eingefügt werden können.

## <a name="retrieve-the-current-user-in-an-aspnet-core-app"></a>Abrufen des aktuellen Benutzers in einer ASP.net Core-App

Es gibt mehrere Optionen zum Abrufen der aktuellen authentifizierten Benutzer `ClaimsPrincipal` in ASP.net Core anstelle von `ClaimsPrincipal.Current` :

* **Controllerbase. User** . MVC-Controller können mit Ihrer [User](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.user) -Eigenschaft auf den aktuellen authentifizierten Benutzer zugreifen.
* **HttpContext. User** . Komponenten mit Zugriff auf die aktuelle `HttpContext` (z. b. Middleware) können die aktuellen Benutzer `ClaimsPrincipal` von " [HttpContext. User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user)" abrufen.
* **Übergeben vom** Aufrufer. Bibliotheken ohne Zugriff auf die aktuelle `HttpContext` werden häufig von Controllern oder middlewarekomponenten aufgerufen, und die Identität des aktuellen Benutzers kann als Argument übermittelt werden.
* **Ihttpcontextaccessor** . Das Projekt, das zu ASP.net Core migriert wird, ist möglicherweise zu groß, um die Identität des aktuellen Benutzers an alle erforderlichen Orte zu übergeben. In solchen Fällen kann [ihttpcontextaccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor) als Problem Umgehung verwendet werden. `IHttpContextAccessor` kann auf die aktuelle zugreifen `HttpContext` (sofern vorhanden). Wenn di verwendet wird, finden Sie weitere Informationen unter <xref:fundamentals/httpcontext> . Eine kurzfristige Lösung, um die Identität des aktuellen Benutzers im Code zu erhalten, der noch nicht aktualisiert wurde, um mit der ASP.net Core di-gesteuerten Architektur zu arbeiten, wäre Folgendes:

  * Stellen Sie `IHttpContextAccessor` im di-Container durch Aufrufen von [addhttpcontextaccessor](https://github.com/aspnet/Hosting/issues/793) in verfügbar `Startup.ConfigureServices` .
  * Beim Start eine Instanz von erhalten `IHttpContextAccessor` und in einer statischen Variablen speichern. Die-Instanz wird für Code verfügbar gemacht, der zuvor den aktuellen Benutzer aus einer statischen Eigenschaft abgerufen hat.
  * Rufen Sie die mit dem des aktuellen Benutzers ab `ClaimsPrincipal` `HttpContextAccessor.HttpContext?.User` . Wenn dieser Code außerhalb des Kontexts einer HTTP-Anforderung verwendet wird, `HttpContext` ist der NULL.

Die letzte Option, die eine Instanz verwendet, die `IHttpContextAccessor` in einer statischen Variablen gespeichert ist, steht im Gegensatz zum ASP.net Core Prinzip, dass injizierte Abhängigkeiten an statische Abhängigkeiten bevorzugt werden. Planen Sie, dass Sie `IHttpContextAccessor` stattdessen Instanzen von di abrufen. Ein statisches Hilfsprogramm kann jedoch nützlich sein, wenn Sie große vorhandene ASP.net-apps migrieren, die verwenden `ClaimsPrincipal.Current` .
