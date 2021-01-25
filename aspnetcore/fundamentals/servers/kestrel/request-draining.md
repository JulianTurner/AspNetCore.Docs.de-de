---
title: Anforderungsausgleich mit ASP.NET Core-Kestrel-Webserver
author: rick-anderson
description: In diesem Artikel erhalten Sie Informationen zum Anforderungsausgleich mit Kestrel, dem plattformübergreifenden Webserver für ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel/request-draining
ms.openlocfilehash: 41d517dae939ad0a83a3402e72eefc4e9db7b32e
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253845"
---
# <a name="request-draining-with-aspnet-core-kestrel-web-server"></a><span data-ttu-id="68127-103">Anforderungsausgleich mit ASP.NET Core-Kestrel-Webserver</span><span class="sxs-lookup"><span data-stu-id="68127-103">Request draining with ASP.NET Core Kestrel web server</span></span>

<span data-ttu-id="68127-104">Das Öffnen von HTTP-Verbindungen ist zeitaufwendig.</span><span class="sxs-lookup"><span data-stu-id="68127-104">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="68127-105">Bei HTTPS ist es zudem ressourcenintensiv.</span><span class="sxs-lookup"><span data-stu-id="68127-105">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="68127-106">Daher versucht Kestrel, Verbindungen gemäß dem HTTP/1.1-Protokoll wiederzuverwenden.</span><span class="sxs-lookup"><span data-stu-id="68127-106">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="68127-107">Ein Anforderungstext muss vollständig genutzt worden sein, damit die Verbindung wiederverwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="68127-107">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="68127-108">Die App nutzt nicht immer den Anforderungstext, z. B. bei POST-HTTP-Anforderungen, bei der der Server eine Umleitung oder 404-Antwort zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="68127-108">The app doesn't always consume the request body, such as HTTP POST requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="68127-109">Im Fall einer HTTP-POST-Weiterleitung gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="68127-109">In the HTTP POST redirect case:</span></span>

* <span data-ttu-id="68127-110">Der Client hat möglicherweise bereits einen Teil der POST-Daten gesendet.</span><span class="sxs-lookup"><span data-stu-id="68127-110">The client may already have sent part of the POST data.</span></span>
* <span data-ttu-id="68127-111">Der Server schreibt die 301-Antwort.</span><span class="sxs-lookup"><span data-stu-id="68127-111">The server writes the 301 response.</span></span>
* <span data-ttu-id="68127-112">Die Verbindung kann erst dann für eine neue Anforderung verwendet werden, wenn die POST-Daten im vorherigen Anforderungstext vollständig gelesen wurden.</span><span class="sxs-lookup"><span data-stu-id="68127-112">The connection can't be used for a new request until the POST data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="68127-113">Kestrel versucht, den Anforderungstext zu leeren.</span><span class="sxs-lookup"><span data-stu-id="68127-113">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="68127-114">Leeren des Anforderungstexts bedeutet, die Daten zu lesen und zu verwerfen, ohne sie zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="68127-114">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="68127-115">Beim Ausgleichsprozess wird ein Kompromiss zwischen der Möglichkeit der Wiederverwendung der Verbindung und der Dauer gefunden, die zum Entfernen der verbleibenden Daten benötigt wird:</span><span class="sxs-lookup"><span data-stu-id="68127-115">The draining process makes a tradeoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="68127-116">Für das Leeren gilt ein Zeitlimit von fünf Sekunden, das nicht konfigurierbar ist.</span><span class="sxs-lookup"><span data-stu-id="68127-116">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="68127-117">Wenn vor dem Zeitlimit nicht alle durch den Header `Content-Length` oder `Transfer-Encoding` angegebenen Daten gelesen wurden, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="68127-117">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="68127-118">Mitunter möchten Sie die Anforderung sofort beenden, entweder bevor oder nachdem die Antwort geschrieben wurde.</span><span class="sxs-lookup"><span data-stu-id="68127-118">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="68127-119">Für Clients bestehen möglicherweise beschränkende Obergrenzen für Daten.</span><span class="sxs-lookup"><span data-stu-id="68127-119">For example, clients may have restrictive data caps.</span></span> <span data-ttu-id="68127-120">Das Einschränken hochgeladener Daten kann zur Priorität werden.</span><span class="sxs-lookup"><span data-stu-id="68127-120">Limiting uploaded data might be a priority.</span></span> <span data-ttu-id="68127-121">Um in solchen Fällen eine Anforderung zu beenden, rufen Sie [HttpContext.abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) in einem Controller, eine Razor Page oder Middleware auf.</span><span class="sxs-lookup"><span data-stu-id="68127-121">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="68127-122">Gegen den Aufruf von `Abort` gibt es Vorbehalte:</span><span class="sxs-lookup"><span data-stu-id="68127-122">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="68127-123">Das Erstellen neuer Verbindungen kann langsam und aufwendig sein.</span><span class="sxs-lookup"><span data-stu-id="68127-123">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="68127-124">Es gibt keine Garantie, dass der Client die Antwort gelesen hat, bevor die Verbindung geschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="68127-124">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="68127-125">Das Aufrufen von `Abort` sollte selten erfolgen und schweren Fehlerfällen und nicht gewöhnlichen Fehlern vorbehalten sein.</span><span class="sxs-lookup"><span data-stu-id="68127-125">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="68127-126">Rufen Sie `Abort` nur dann auf, wenn ein konkretes Problem gelöst werden muss.</span><span class="sxs-lookup"><span data-stu-id="68127-126">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="68127-127">Rufen Sie beispielsweise `Abort` auf, wenn böswillige Clients versuchen, Daten per POST abzurufen, oder wenn es einen Fehler im Clientcode gibt, der umfangreiche oder zahlreiche Anforderungen verursacht.</span><span class="sxs-lookup"><span data-stu-id="68127-127">For example, call `Abort` if malicious clients are trying to POST data or when there's a bug in client code that causes large or several requests.</span></span>
  * <span data-ttu-id="68127-128">Rufen Sie `Abort` nicht für gewöhnliche Fehlersituationen auf, wie z. B. HTTP 404 (Nicht gefunden).</span><span class="sxs-lookup"><span data-stu-id="68127-128">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="68127-129">Durch den Aufruf von [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) vor dem Aufruf von `Abort` wird sichergestellt, dass der Server das Schreiben der Antwort abgeschlossen hat.</span><span class="sxs-lookup"><span data-stu-id="68127-129">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="68127-130">Das Clientverhalten ist jedoch nicht vorhersagbar, und es kann sein, dass die Antwort nicht gelesen wird, bevor die Verbindung abgebrochen wird.</span><span class="sxs-lookup"><span data-stu-id="68127-130">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="68127-131">Dieser Prozess bei HTTP/2 ist anders, da das Protokoll den Abbruch einzelner Anforderungsströme ohne Schließen der Verbindung unterstützt.</span><span class="sxs-lookup"><span data-stu-id="68127-131">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="68127-132">Das fünfsekündige Zeitlimit für Ausgleiche gilt nicht.</span><span class="sxs-lookup"><span data-stu-id="68127-132">The five-second drain timeout doesn't apply.</span></span> <span data-ttu-id="68127-133">Wenn nach dem Fertigstellen einer Antwort ungelesene Anforderungstextdaten vorhanden sind, sendet der Server einen HTTP/2-RST-Datenrahmen.</span><span class="sxs-lookup"><span data-stu-id="68127-133">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="68127-134">Zusätzliche Datenrahmen im Anforderungstext werden ignoriert.</span><span class="sxs-lookup"><span data-stu-id="68127-134">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="68127-135">Wenn möglich, ist es für Clients besser, den Anforderungsheader [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) zu verwenden und auf die Antwort des Servers zu warten, bevor mit dem Senden des Anforderungstexts begonnen wird.</span><span class="sxs-lookup"><span data-stu-id="68127-135">If possible, it's better for clients to use the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="68127-136">Das gibt dem Client die Gelegenheit, die Antwort zu prüfen und abzubrechen, bevor nicht benötigte Daten gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="68127-136">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>
