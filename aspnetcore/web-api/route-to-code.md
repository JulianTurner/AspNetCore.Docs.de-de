---
title: Grundlegende JSON-APIs mit Route-to-code in ASP.net Core
author: jamesnk
description: Erfahren Sie, wie Sie Route-to-code JSON-Erweiterungs Methoden und verwenden, um Lightweight JSON-Web-APIs zu erstellen.
monikerRange: '>= aspnetcore-5.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 11/30/2020
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
- Route-to-code
uid: web-api/route-to-code
ms.openlocfilehash: f8a3804a887ebfa0f5284d8991e903c978b18208
ms.sourcegitcommit: 92439194682dc788b8b5b3a08bd2184dc00e200b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96556605"
---
# <a name="basic-json-apis-with-no-locroute-to-code-in-aspnet-core"></a>Grundlegende JSON-APIs mit Route-to-code in ASP.net Core

Von [James Newton-King](https://github.com/jamesnk)

ASP.net Core unterstützt eine Reihe von Methoden zum Erstellen von JSON-Web-APIs:

* [ASP.net Core Web-API](xref:web-api/index) bietet ein umfassendes Framework zum Erstellen von APIs. Ein Dienst wird erstellt, indem von geerbt wird <xref:Microsoft.AspNetCore.Mvc.ControllerBase> . Einige Features, die vom Framework bereitgestellt werden, sind Modell Bindung, Validierung, Inhaltsaushandlung, Eingabe-und Ausgabe Formatierung und OpenAPI.
* Route-to-code ist eine Alternative zum ASP.net Core Web-API. Route-to-code verbindet [ASP.net Core Routing](xref:fundamentals/routing) direkt mit Ihrem Code. Der Code liest aus der Anforderung und schreibt die Antwort. Route-to-code verfügt nicht über die erweiterten Features der Web-API, aber es ist auch keine Konfiguration erforderlich, um Sie zu verwenden.

Route-to-code ist ein guter Ansatz beim Aufbau von kleinen und grundlegenden JSON-Web-APIs.

## <a name="create-json-web-apis"></a>Erstellen von JSON-Web-APIs

ASP.net Core stellt Hilfsmethoden bereit, die die Erstellung von JSON-Web-APIs vereinfachen:

* <xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.HasJsonContentType%2A> überprüft den `Content-Type` Header auf einen JSON-Inhaltstyp.
* <xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.ReadFromJsonAsync%2A> liest JSON aus der Anforderung und deserialisiert Sie in den angegebenen Typ.
* <xref:Microsoft.AspNetCore.Http.HttpResponseJsonExtensions.WriteAsJsonAsync%2A> schreibt den angegebenen Wert als JSON in den Antworttext und legt den Inhaltstyp der Antwort auf fest `application/json` .

In *Startup.cs* werden Lightweight, Routen basierte JSON-APIs angegeben. Die Route und die API-Logik werden in <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> als Teil der Anforderungs Pipeline einer App konfiguriert.

### <a name="write-json-response"></a>JSON-Antwortschreiben

Beachten Sie den folgenden Code, der eine JSON-API für eine App konfiguriert:

[!code-csharp[](route-to-code/sample/Startup3.cs?name=snippet&highlight=6)]

Der obige Code:

* Fügt einen HTTP Get-API-Endpunkt mit `/hello/{name:alpha}` als Routen Vorlage hinzu.
* Wenn die Route abgeglichen wird, liest die API den `name` Routen Wert aus der Anforderung.
* Schreibt einen anonymen Typ als JSON-Antwort mit `WriteAsJsonAsync` .

### <a name="read-json-request"></a>JSON-Anforderung lesen

`HasJsonContentType` und `ReadFromJsonAsync` können verwendet werden, um eine JSON-Antwort in einer Routen basierten JSON-API zu deserialisieren:

[!code-csharp[](route-to-code/sample/Startup2.cs?name=snippet&highlight=5,11)]

Der obige Code:

* Fügt einen HTTP Post-API-Endpunkt mit `/weather` als Routen Vorlage hinzu.
* Wenn die Route übereinstimmt, wird `HasJsonContentType` der Inhaltstyp der Anforderung überprüft. Ein nicht-JSON-Inhaltstyp gibt einen 415-Statuscode zurück.
* Wenn der Inhaltstyp JSON ist, wird der Anforderungs Inhalt von deserialisiert `ReadFromJsonAsync` .

### <a name="configure-json-serialization"></a>Konfigurieren der JSON-Serialisierung

Es gibt zwei Möglichkeiten zum Anpassen der JSON-Serialisierung:

* Standardserialisierungsoptionen können mit <xref:Microsoft.AspNetCore.Http.Json.JsonOptions> in der-Methode konfiguriert werden `Startup.ConfigureServices` .
* `WriteAsJsonAsync` und `ReadFromJsonAsync` verfügen über über Ladungen, die ein- <xref:System.Text.Json.JsonSerializerOptions> Objekt akzeptieren. Dieses Options-Objekt überschreibt die Standardoptionen.

[!code-csharp[](route-to-code/sample/Startup6.cs?name=snippet)]

## <a name="authentication-and-authorization"></a>Authentifizierung und Autorisierung

Route-to-code unterstützt Authentifizierung und Autorisierung. Attribute, wie z. b. `[Authorize]` und `[AllowAnonymous]` , können nicht auf Endpunkten platziert werden, die einem Anforderungs Delegaten zugeordnet sind. Stattdessen werden Autorisierungs Metadaten mithilfe der <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization%2A> -und- <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.AllowAnonymous%2A> Erweiterungs Methoden hinzugefügt.

[!code-csharp[](route-to-code/sample/Startup.cs?name=snippet&highlight=30)]

## <a name="dependency-injection"></a>Dependency Injection

[Abhängigkeitsinjektion (di)](xref:fundamentals/dependency-injection) mit einem Konstruktor ist mit nicht möglich Route-to-code . Die Web-API erstellt einen Controller für Sie mit Diensten, die in den Konstruktor eingefügt werden. Ein Typ wird nicht erstellt, wenn ein Endpunkt ausgeführt wird, sodass Dienste manuell aufgelöst werden müssen.

Routen basierte APIs können <xref:System.IServiceProvider> zum Auflösen von Diensten verwendet werden:

* Vorübergehende und Bereichs bezogene Lebensdauer Dienste wie z `DbContext` . b. müssen aus [HttpContext. requestservices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) innerhalb des Anforderungs Delegaten eines Endpunkts aufgelöst werden.
* Singleton-Lebensdauer Dienste, wie z `ILogger` . b., können von [iendpointroutebuilder. Service Provider](xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.ServiceProvider)aufgelöst werden. Dienste können außerhalb von Anforderungs Delegaten aufgelöst und von Endpunkten gemeinsam genutzt werden.

[!code-csharp[](route-to-code/sample/Startup4.cs?name=snippet&highlight=3,7)]

APIs, die di ausgiebig verwenden, sollten einen ASP.net Core App-Typ verwenden, der di unterstützt. Beispielsweise ASP.net Core Web-API. Die Dienst Injektion mithilfe des Konstruktors eines Controllers ist einfacher als das manuelle Auflösen von Diensten.

## <a name="api-project-structure"></a>API-Projektstruktur

Routen basierte APIs müssen in *Startup.cs* nicht gefunden werden. APIs können in anderen Dateien abgelegt und beim Start mit zugeordnet werden `UseEndpoints` . Bei diesem Ansatz wird die Größe der Start Konfigurationsdatei reduziert.

Beachten Sie die folgende statische `UserApi` Klasse, die eine `Map` Methode definiert. Die-Methode ordnet Routen basierte APIs zu.

[!code-csharp[](route-to-code/sample/UserApi.cs?name=snippet)]

In der `Startup.Configure` `Map` -Methode werden die statischen Methoden der-Methode und der anderen Klasse in aufgerufen `UseEndpoints` :

[!code-csharp[](route-to-code/sample/Startup5.cs?name=snippet)]

## <a name="notable-missing-features-compared-to-web-api"></a>Wichtige fehlende Features im Vergleich zur Web-API

Route-to-code ist für grundlegende JSON-APIs konzipiert. Sie verfügt nicht über Unterstützung für viele der erweiterten Features, die von ASP.net Core Web-API bereitgestellt werden.

Folgende Funktionen werden nicht bereitgestellt Route-to-code :

* Modellbindung
* Modellvalidierung
* OpenAPI/Swagger
* Inhaltsaushandlung
* Injektion von konstruktorabhängigkeiten
* `ProblemDetails` ([RFC 7807](https://tools.ietf.org/html/rfc7807))

Verwenden Sie ggf. [ASP.net Core Web-API](xref:web-api/index) , um eine API zu erstellen, wenn Sie einige der Features in der vorangehenden Liste erfordert.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:web-api/index>
* <xref:fundamentals/routing>
* <xref:fundamentals/dependency-injection>
