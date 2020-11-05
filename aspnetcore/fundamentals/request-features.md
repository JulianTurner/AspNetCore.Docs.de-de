---
title: Anforderungsfeatures in ASP.NET Core
author: ardalis
description: Erfahren Sie mehr zur Webserverimplementierung, die mit HTTP-Anforderungen und -antworten in Verbindung stehen, die in Schnittstellen definiert werden.
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
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
uid: fundamentals/request-features
ms.openlocfilehash: d906474b0fd291cc4a68390f390b2bf538e21eff
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053708"
---
# <a name="request-features-in-aspnet-core"></a><span data-ttu-id="8d931-103">Anforderungsfeatures in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8d931-103">Request Features in ASP.NET Core</span></span>

<span data-ttu-id="8d931-104">Von [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="8d931-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="8d931-105">Die `HttpContext`-API, mit der Anwendungen und Middleware Anforderungen verarbeiten, verfügt über eine zugrunde liegende Abstraktionsebene in Form von sogenannten *Featureschnittstellen*.</span><span class="sxs-lookup"><span data-stu-id="8d931-105">The `HttpContext` API that applications and middleware use to process requests has an abstraction layer undernieth it called *feature interfaces*.</span></span> <span data-ttu-id="8d931-106">Jede Featureschnittstelle bietet eine differenzierte Teilmenge der Funktionen, die von `HttpContext` verfügbar gemacht werden.</span><span class="sxs-lookup"><span data-stu-id="8d931-106">Each feature interface provides a granular subset of the functionality exposed by `HttpContext`.</span></span> <span data-ttu-id="8d931-107">Diese Schnittstellen können vom Server oder der Middleware während der Verarbeitung der Anforderung hinzugefügt, geändert, umschlossen, ersetzt oder sogar entfernt werden, ohne dass der gesamte `HttpContext` erneut implementiert werden muss.</span><span class="sxs-lookup"><span data-stu-id="8d931-107">These interfaces can be added, modified, wrapped, replaced, or even removed by the server or middleware as the request is processed without having to re-implement the entire `HttpContext`.</span></span> <span data-ttu-id="8d931-108">Sie können auch verwendet werden, um bei Tests Funktionen zu simulieren.</span><span class="sxs-lookup"><span data-stu-id="8d931-108">They can also be used to mock functionality when testing.</span></span>

## <a name="feature-collections"></a><span data-ttu-id="8d931-109">Featuresammlung</span><span class="sxs-lookup"><span data-stu-id="8d931-109">Feature collections</span></span>

<span data-ttu-id="8d931-110">Die <xref:Microsoft.AspNetCore.Http.HttpContext.Features>-Eigenschaft von `HttpContext` bietet Zugriff auf die Sammlung von Featureschnittstellen für die aktuelle Anforderung.</span><span class="sxs-lookup"><span data-stu-id="8d931-110">The <xref:Microsoft.AspNetCore.Http.HttpContext.Features> property of `HttpContext` provides access to the collection of feature interfaces for the current request.</span></span> <span data-ttu-id="8d931-111">Da die Featuresammlung auch im Kontext einer Anforderung änderbar ist, kann Middleware verwendet werden, um die Sammlung zu modifizieren und Unterstützung für zusätzliche Features hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="8d931-111">Since the feature collection is mutable even within the context of a request, middleware can be used to modify the collection and add support for additional features.</span></span> <span data-ttu-id="8d931-112">Einige erweiterte Features sind nur verfügbar, wenn Sie über die Featuresammlung auf die zugehörige Schnittstelle zugreifen.</span><span class="sxs-lookup"><span data-stu-id="8d931-112">Some advanced features are only available by accessing the associated interface through the feature collection.</span></span>

## <a name="feature-interfaces"></a><span data-ttu-id="8d931-113">Featureschnittstellen</span><span class="sxs-lookup"><span data-stu-id="8d931-113">Feature interfaces</span></span>

<span data-ttu-id="8d931-114">ASP.NET Core definiert einige allgemeine HTTP-Featureschnittstellen in <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName> mit deren Hilfe die verschiedenen Server und Middleware die unterstützten Features ermitteln können.</span><span class="sxs-lookup"><span data-stu-id="8d931-114">ASP.NET Core defines a number of common HTTP feature interfaces in <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName>, which are shared by various servers and middleware to identify the features that they support.</span></span> <span data-ttu-id="8d931-115">Server und Middleware können auch eigene Schnittstellen mit zusätzlichen Funktionen bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="8d931-115">Servers and middleware may also provide their own interfaces with additional functionality.</span></span>

<span data-ttu-id="8d931-116">Die meisten Featureschnittstellen bieten optionale, ergänzende Funktionen, und die zugehörigen `HttpContext`-APIs stellen Standardwerte bereit, wenn das Feature nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="8d931-116">Most feature interfaces provide optional, light-up functionality, and their associated `HttpContext` APIs provide defaults if the feature isn't present.</span></span> <span data-ttu-id="8d931-117">Einige Schnittstellen werden im Folgenden als erforderlich dargestellt, da sie wichtige Anforderungs- und Antwortfunktionen bereitstellen und implementiert werden müssen, um die Anforderung zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="8d931-117">A few interfaces are indicated in the following content as required because they provide core request and response functionality and must be implemented in order to process the request.</span></span>

<span data-ttu-id="8d931-118">Die folgenden Featureschnittstellen stammen von <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName>:</span><span class="sxs-lookup"><span data-stu-id="8d931-118">The following feature interfaces are from <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName>:</span></span>

<span data-ttu-id="8d931-119"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature>: Definiert die Struktur einer HTTP-Anforderung, einschließlich Protokoll, Pfad, Abfragezeichenfolge, Header und Text.</span><span class="sxs-lookup"><span data-stu-id="8d931-119"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature>: Defines the structure of an HTTP request, including the protocol, path, query string, headers, and body.</span></span> <span data-ttu-id="8d931-120">Dieses Feature ist erforderlich, um Anforderungen zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="8d931-120">This feature is required in order to process requests.</span></span>

<span data-ttu-id="8d931-121"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature>: Definiert die Struktur einer HTTP-Antwort, einschließlich Statuscode, Header und Antworttext.</span><span class="sxs-lookup"><span data-stu-id="8d931-121"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature>: Defines the structure of an HTTP response, including the status code, headers, and body of the response.</span></span> <span data-ttu-id="8d931-122">Dieses Feature ist erforderlich, um Anforderungen zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="8d931-122">This feature is required in order to process requests.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8d931-123"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseBodyFeature>: Definiert verschiedene Methoden zum Schreiben des Antworttexts, wobei ein `Stream`, ein `PipeWriter` oder eine Datei verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="8d931-123"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseBodyFeature>: Defines different ways of writing out the response body, using either a `Stream`, a `PipeWriter`, or a file.</span></span> <span data-ttu-id="8d931-124">Dieses Feature ist erforderlich, um Anforderungen zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="8d931-124">This feature is required in order to process requests.</span></span> <span data-ttu-id="8d931-125">Hierdurch werden `IHttpResponseFeature.Body` und `IHttpSendFileFeature` ersetzt.</span><span class="sxs-lookup"><span data-stu-id="8d931-125">This replaces `IHttpResponseFeature.Body` and `IHttpSendFileFeature`.</span></span>

::: moniker-end

<span data-ttu-id="8d931-126"><xref:Microsoft.AspNetCore.Http.Features.Authentication.IHttpAuthenticationFeature>: Enthält den der Anforderung derzeit zugeordneten <xref:System.Security.Claims.ClaimsPrincipal>.</span><span class="sxs-lookup"><span data-stu-id="8d931-126"><xref:Microsoft.AspNetCore.Http.Features.Authentication.IHttpAuthenticationFeature>: Holds the <xref:System.Security.Claims.ClaimsPrincipal> currently associated with the request.</span></span>

<span data-ttu-id="8d931-127"><xref:Microsoft.AspNetCore.Http.Features.IFormFeature>: Dient zum Analysieren und Zwischenspeichern eingehender HTTP- und mehrteiliger Formularübermittlungen.</span><span class="sxs-lookup"><span data-stu-id="8d931-127"><xref:Microsoft.AspNetCore.Http.Features.IFormFeature>: Used to parse and cache incoming HTTP and multipart form submissions.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="8d931-128"><xref:Microsoft.AspNetCore.Http.Features.IHttpBodyControlFeature>: Steuert, ob synchrone E/A-Vorgänge für den Anforderungs- oder Antworttext zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="8d931-128"><xref:Microsoft.AspNetCore.Http.Features.IHttpBodyControlFeature>: Used to control if synchronous IO operations are allowed for the request or response bodies.</span></span>

::: moniker-end
   
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8d931-129"><xref:Microsoft.AspNetCore.Http.Features.IHttpBufferingFeature>: Definiert Methoden zum Deaktivieren des Pufferns von Anforderungen und/oder Antworten.</span><span class="sxs-lookup"><span data-stu-id="8d931-129"><xref:Microsoft.AspNetCore.Http.Features.IHttpBufferingFeature>: Defines methods for disabling buffering of requests and/or responses.</span></span>

::: moniker-end

<span data-ttu-id="8d931-130"><xref:Microsoft.AspNetCore.Http.Features.IHttpConnectionFeature>: Definiert Eigenschaften für die Verbindungs-ID, lokale und Remoteadressen sowie Ports.</span><span class="sxs-lookup"><span data-stu-id="8d931-130"><xref:Microsoft.AspNetCore.Http.Features.IHttpConnectionFeature>: Defines properties for the connection id and local and remote addresses and ports.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="8d931-131"><xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>: Steuert die maximal zulässige Größe des Anforderungstexts für die aktuelle Anforderung.</span><span class="sxs-lookup"><span data-stu-id="8d931-131"><xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>: Controls the maximum allowed request body size for the current request.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="8d931-132">`IHttpRequestBodyDetectionFeature`: Gibt an, ob die Anforderung einen Text enthalten kann.</span><span class="sxs-lookup"><span data-stu-id="8d931-132">`IHttpRequestBodyDetectionFeature`: Indicates if the request can have a body.</span></span>

::: moniker-end

<span data-ttu-id="8d931-133"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestIdentifierFeature>: Fügt eine Eigenschaft hinzu, die implementiert werden kann, um eine Anforderung eindeutig zu identifizieren.</span><span class="sxs-lookup"><span data-stu-id="8d931-133"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestIdentifierFeature>: Adds a property that can be implemented to uniquely identify requests.</span></span>

<span data-ttu-id="8d931-134"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestLifetimeFeature>: Definiert Unterstützung für das Abbrechen von Verbindungen oder das Erkennen einer verfrühten Beendigung einer Anforderung, z. B. durch eine abgebrochene Verbindung mit dem Client.</span><span class="sxs-lookup"><span data-stu-id="8d931-134"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestLifetimeFeature>: Defines support for aborting connections or detecting if a request has been terminated prematurely, such as by a client disconnect.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8d931-135"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestTrailersFeature>: Ermöglicht Zugriff auf die Nachspannheader der Anforderung, sofern vorhanden.</span><span class="sxs-lookup"><span data-stu-id="8d931-135"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestTrailersFeature>: Provides access to the request trailer headers, if any.</span></span>

<span data-ttu-id="8d931-136"><xref:Microsoft.AspNetCore.Http.Features.IHttpResetFeature>: Wird zum Senden von Zurücksetzungsmeldungen für Protokolle verwendet, die diese unterstützen, z. B. HTTP/2 oder HTTP/3.</span><span class="sxs-lookup"><span data-stu-id="8d931-136"><xref:Microsoft.AspNetCore.Http.Features.IHttpResetFeature>: Used to send reset messages for protocols that support them such as HTTP/2 or HTTP/3.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="8d931-137"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseTrailersFeature>: Ermöglicht der Anwendung, Nachspannheader in der Antwort anzugeben, sofern diese unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="8d931-137"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseTrailersFeature>: Enables the application to provide response trailer headers if supported.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8d931-138"><xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>: Definiert eine Methode zum asynchronen Senden von Dateien.</span><span class="sxs-lookup"><span data-stu-id="8d931-138"><xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>: Defines a method for sending files asynchronously.</span></span>

::: moniker-end

<span data-ttu-id="8d931-139"><xref:Microsoft.AspNetCore.Http.Features.IHttpUpgradeFeature>: Definiert Unterstützung für [HTTP-Upgrades](https://tools.ietf.org/html/rfc2616.html#section-14.42), mit denen der Client festlegen kann, welche zusätzlichen Protokolle er verwenden möchte, wenn der Server Protokolle wechseln möchte.</span><span class="sxs-lookup"><span data-stu-id="8d931-139"><xref:Microsoft.AspNetCore.Http.Features.IHttpUpgradeFeature>: Defines support for [HTTP Upgrades](https://tools.ietf.org/html/rfc2616.html#section-14.42), which allow the client to specify which additional protocols it would like to use if the server wishes to switch protocols.</span></span>

<span data-ttu-id="8d931-140"><xref:Microsoft.AspNetCore.Http.Features.IHttpWebSocketFeature>: Definiert eine API zur Unterstützung von Websockets.</span><span class="sxs-lookup"><span data-stu-id="8d931-140"><xref:Microsoft.AspNetCore.Http.Features.IHttpWebSocketFeature>: Defines an API for supporting web sockets.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8d931-141"><xref:Microsoft.AspNetCore.Http.Features.IHttpsCompressionFeature>: Steuert, ob über HTTPS-Verbindungen eine Antwortkomprimierung verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="8d931-141"><xref:Microsoft.AspNetCore.Http.Features.IHttpsCompressionFeature>: Controls if response compression should be used over HTTPS connections.</span></span>

::: moniker-end

<span data-ttu-id="8d931-142"><xref:Microsoft.AspNetCore.Http.Features.IItemsFeature>: Speichert die <xref:Microsoft.AspNetCore.Http.Features.IItemsFeature.Items>-Sammlung für den Anwendungszustand pro Anforderung.</span><span class="sxs-lookup"><span data-stu-id="8d931-142"><xref:Microsoft.AspNetCore.Http.Features.IItemsFeature>: Stores the <xref:Microsoft.AspNetCore.Http.Features.IItemsFeature.Items> collection for per request application state.</span></span>

<span data-ttu-id="8d931-143"><xref:Microsoft.AspNetCore.Http.Features.IQueryFeature>: Analysiert die Abfragezeichenfolge und speichert sie zwischen.</span><span class="sxs-lookup"><span data-stu-id="8d931-143"><xref:Microsoft.AspNetCore.Http.Features.IQueryFeature>: Parses and caches the query string.</span></span>
   
::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8d931-144"><xref:Microsoft.AspNetCore.Http.Features.IRequestBodyPipeFeature>: Stellt den Anforderungstext als einen <xref:System.IO.Pipelines.PipeReader> dar.</span><span class="sxs-lookup"><span data-stu-id="8d931-144"><xref:Microsoft.AspNetCore.Http.Features.IRequestBodyPipeFeature>: Represents the request body as a <xref:System.IO.Pipelines.PipeReader>.</span></span>
 
::: moniker-end

<span data-ttu-id="8d931-145"><xref:Microsoft.AspNetCore.Http.Features.IRequest:::no-loc(Cookie):::sFeature>: Analysiert die `:::no-loc(Cookie):::`-Headerwerte der Anforderung und speichert sie zwischen.</span><span class="sxs-lookup"><span data-stu-id="8d931-145"><xref:Microsoft.AspNetCore.Http.Features.IRequest:::no-loc(Cookie):::sFeature>: Parses and caches the request `:::no-loc(Cookie):::` header values.</span></span>

<span data-ttu-id="8d931-146"><xref:Microsoft.AspNetCore.Http.Features.IResponse:::no-loc(Cookie):::sFeature>: Steuert, wie die :::no-loc(cookie)::: der Antwort auf den `Set-:::no-loc(Cookie):::`-Header angewandt werden.</span><span class="sxs-lookup"><span data-stu-id="8d931-146"><xref:Microsoft.AspNetCore.Http.Features.IResponse:::no-loc(Cookie):::sFeature>: Controls how response :::no-loc(cookie):::s are applied to the `Set-:::no-loc(Cookie):::` header.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="8d931-147"><xref:Microsoft.AspNetCore.Http.Features.IServerVariablesFeature>: Dieses Feature ermöglicht den Zugriff auf Servervariablen der Anforderung, z. B. die von IIS bereitgestellten.</span><span class="sxs-lookup"><span data-stu-id="8d931-147"><xref:Microsoft.AspNetCore.Http.Features.IServerVariablesFeature>: This feature provides access to request server variables such as those provided by IIS.</span></span>

::: moniker-end
   
<span data-ttu-id="8d931-148"><xref:Microsoft.AspNetCore.Http.Features.IServiceProvidersFeature>: Ermöglicht den Zugriff auf einen <xref:System.IServiceProvider> mit bereichsspezifischen Anforderungsdiensten.</span><span class="sxs-lookup"><span data-stu-id="8d931-148"><xref:Microsoft.AspNetCore.Http.Features.IServiceProvidersFeature>: Provides access to an <xref:System.IServiceProvider> with scoped request services.</span></span>

<span data-ttu-id="8d931-149"><xref:Microsoft.AspNetCore.Http.Features.ISessionFeature>: Definiert die Abstraktionen `ISessionFactory` und <xref:Microsoft.AspNetCore.Http.ISession> zur Unterstützung von Benutzersitzungen.</span><span class="sxs-lookup"><span data-stu-id="8d931-149"><xref:Microsoft.AspNetCore.Http.Features.ISessionFeature>: Defines `ISessionFactory` and <xref:Microsoft.AspNetCore.Http.ISession> abstractions for supporting user sessions.</span></span> <span data-ttu-id="8d931-150">`ISessionFeature` wird von <xref:Microsoft.AspNetCore.Session.SessionMiddleware> implementiert (siehe <xref:fundamentals/app-state>).</span><span class="sxs-lookup"><span data-stu-id="8d931-150">`ISessionFeature` is implemented by the <xref:Microsoft.AspNetCore.Session.SessionMiddleware> (see <xref:fundamentals/app-state>).</span></span>

<span data-ttu-id="8d931-151"><xref:Microsoft.AspNetCore.Http.Features.ITlsConnectionFeature>: Definiert eine API zum Abrufen von Clientzertifikaten.</span><span class="sxs-lookup"><span data-stu-id="8d931-151"><xref:Microsoft.AspNetCore.Http.Features.ITlsConnectionFeature>: Defines an API for retrieving client certificates.</span></span>

<span data-ttu-id="8d931-152"><xref:Microsoft.AspNetCore.Http.Features.ITlsTokenBindingFeature>: Definiert Methoden zum Arbeiten mit TLS-Tokenbindungsparametern.</span><span class="sxs-lookup"><span data-stu-id="8d931-152"><xref:Microsoft.AspNetCore.Http.Features.ITlsTokenBindingFeature>: Defines methods for working with TLS token binding parameters.</span></span>
   
::: moniker range=">= aspnetcore-2.2"
   
<span data-ttu-id="8d931-153"><xref:Microsoft.AspNetCore.Http.Features.ITrackingConsentFeature>: Dient zum Abfragen, Gewähren und Widerrufen der Benutzereinwilligung zur Speicherung von Benutzerinformationen im Zusammenhang mit Aktivitäten und Funktionen der Website.</span><span class="sxs-lookup"><span data-stu-id="8d931-153"><xref:Microsoft.AspNetCore.Http.Features.ITrackingConsentFeature>: Used to query, grant, and withdraw user consent regarding the storage of user information related to site activity and functionality.</span></span>
   
::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="8d931-154">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="8d931-154">Additional resources</span></span>

* <xref:fundamentals/servers/index>
* <xref:fundamentals/middleware/index>
