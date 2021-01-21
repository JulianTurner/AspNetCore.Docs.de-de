---
title: Sicherheitsaspekte in gRPC für ASP.NET Core
author: jamesnk
description: Informationen zu Sicherheitsaspekten für gRPC für ASP.NET Core
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/07/2019
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
uid: grpc/security
ms.openlocfilehash: 45ac0916a368cf68f4d40e14298a7628446989ee
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252811"
---
# <a name="security-considerations-in-grpc-for-aspnet-core"></a><span data-ttu-id="eb826-103">Sicherheitsaspekte in gRPC für ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="eb826-103">Security considerations in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="eb826-104">Von [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="eb826-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="eb826-105">Dieser Artikel enthält Informationen zum Sichern von gRPC mit .NET Core</span><span class="sxs-lookup"><span data-stu-id="eb826-105">This article provides information on securing gRPC with .NET Core.</span></span>

## <a name="transport-security"></a><span data-ttu-id="eb826-106">Transportsicherheit</span><span class="sxs-lookup"><span data-stu-id="eb826-106">Transport security</span></span>

<span data-ttu-id="eb826-107">GRPC-Nachrichten werden über HTTP/2 gesendet und empfangen.</span><span class="sxs-lookup"><span data-stu-id="eb826-107">gRPC messages are sent and received using HTTP/2.</span></span> <span data-ttu-id="eb826-108">Es wird Folgendes empfohlen:</span><span class="sxs-lookup"><span data-stu-id="eb826-108">We recommend:</span></span>

* <span data-ttu-id="eb826-109">[Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) wird verwendet, um Nachrichten in gRPC-Produktions-Apps zu sichern.</span><span class="sxs-lookup"><span data-stu-id="eb826-109">[Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) be used to secure messages in production gRPC apps.</span></span>
* <span data-ttu-id="eb826-110">GRPC-Dienste dürfen nur über gesicherte Ports lauschen und reagieren.</span><span class="sxs-lookup"><span data-stu-id="eb826-110">gRPC services should only listen and respond over secured ports.</span></span>

::: moniker range=">= aspnetcore-5.0"
<span data-ttu-id="eb826-111">TLS ist in Kestrel konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="eb826-111">TLS is configured in Kestrel.</span></span> <span data-ttu-id="eb826-112">Weitere Informationen zur Kestrel-Endpunktkonfiguration finden Sie unter [Kestrel-Endpunktkonfiguration](xref:fundamentals/servers/kestrel/endpoints).</span><span class="sxs-lookup"><span data-stu-id="eb826-112">For more information on configuring Kestrel endpoints, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel/endpoints).</span></span>
::: moniker-end

::: moniker range="< aspnetcore-5.0"
<span data-ttu-id="eb826-113">TLS ist in Kestrel konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="eb826-113">TLS is configured in Kestrel.</span></span> <span data-ttu-id="eb826-114">Weitere Informationen zur Kestrel-Endpunktkonfiguration finden Sie unter [Kestrel-Endpunktkonfiguration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="eb826-114">For more information on configuring Kestrel endpoints, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>
::: moniker-end

## <a name="exceptions"></a><span data-ttu-id="eb826-115">Ausnahmen</span><span class="sxs-lookup"><span data-stu-id="eb826-115">Exceptions</span></span>

<span data-ttu-id="eb826-116">Ausnahmemeldungen werden generell als vertrauliche Daten angesehen, die nicht für einen Client offengelegt werden dürfen.</span><span class="sxs-lookup"><span data-stu-id="eb826-116">Exception messages are generally considered sensitive data that shouldn't be revealed to a client.</span></span> <span data-ttu-id="eb826-117">Standardmäßig sendet gRPC keine Details einer von einem gRPC-Dienst ausgelösten Ausnahme an den Client.</span><span class="sxs-lookup"><span data-stu-id="eb826-117">By default, gRPC doesn't send the details of an exception thrown by a gRPC service to the client.</span></span> <span data-ttu-id="eb826-118">Stattdessen erhält der Client eine generische Nachricht, die angibt, dass ein Fehler aufgetreten ist.</span><span class="sxs-lookup"><span data-stu-id="eb826-118">Instead, the client receives a generic message indicating an error occurred.</span></span> <span data-ttu-id="eb826-119">Ausnahmemeldungen, die an den Client übermittelt werden, können mithilfe von [EnableDetailedErrors](xref:grpc/configuration#configure-services-options) überschrieben werden (zum Beispiel während der Entwicklung oder eines Tests).</span><span class="sxs-lookup"><span data-stu-id="eb826-119">Exception message delivery to the client can be overridden (for example, in development or test) with [EnableDetailedErrors](xref:grpc/configuration#configure-services-options).</span></span> <span data-ttu-id="eb826-120">Ausnahmemeldungen dürfen dem Client in Produktions-Apps nicht offengelegt werden.</span><span class="sxs-lookup"><span data-stu-id="eb826-120">Exception messages shouldn't be exposed to the client in production apps.</span></span>

## <a name="message-size-limits"></a><span data-ttu-id="eb826-121">Beschränkungen der Nachrichtengröße</span><span class="sxs-lookup"><span data-stu-id="eb826-121">Message size limits</span></span>

<span data-ttu-id="eb826-122">Eingehende Nachrichten an gRPC-Clients und -Dienste werden in den Speicher geladen.</span><span class="sxs-lookup"><span data-stu-id="eb826-122">Incoming messages to gRPC clients and services are loaded into memory.</span></span> <span data-ttu-id="eb826-123">Die Größenbeschränkung für Nachrichten sind Mechanismen, um zu verhindern, dass gRPC übermäßig viele Ressourcen beansprucht.</span><span class="sxs-lookup"><span data-stu-id="eb826-123">Message size limits are a mechanism to help prevent gRPC from consuming excessive resources.</span></span>

<span data-ttu-id="eb826-124">GRPC verwendet Größenbeschränkungen pro Nachricht, um eingehende und ausgehende Nachrichten zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="eb826-124">gRPC uses per-message size limits to manage incoming and outgoing messages.</span></span> <span data-ttu-id="eb826-125">Standardmäßig beschränkt gRPC die eingehenden Nachrichten auf 4 MB.</span><span class="sxs-lookup"><span data-stu-id="eb826-125">By default, gRPC limits incoming messages to 4 MB.</span></span> <span data-ttu-id="eb826-126">Für ausgehende Nachrichten gibt es keine Beschränkung.</span><span class="sxs-lookup"><span data-stu-id="eb826-126">There is no limit on outgoing messages.</span></span>

<span data-ttu-id="eb826-127">Auf dem Server können gRPC-Nachrichtengrenzwerte für alle Dienste in einer App mit `AddGrpc` konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="eb826-127">On the server, gRPC message limits can be configured for all services in an app with `AddGrpc`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 1 * 1024 * 1024; // 1 MB
        options.MaxSendMessageSize = 1 * 1024 * 1024; // 1 MB
    });
}
```

<span data-ttu-id="eb826-128">Beschränkungen können auch für einen einzelnen Dienst mit `AddServiceOptions<TService>` konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="eb826-128">Limits can also be configured for an individual service using `AddServiceOptions<TService>`.</span></span> <span data-ttu-id="eb826-129">Weitere Informationen zur Konfiguration von Beschränkungen der Nachrichtengröße finden Sie unter [gRPC-Konfiguration](xref:grpc/configuration).</span><span class="sxs-lookup"><span data-stu-id="eb826-129">For more information on configuring message size limits, see [gRPC configuration](xref:grpc/configuration).</span></span>

## <a name="client-certificate-validation"></a><span data-ttu-id="eb826-130">Clientzertifikatüberprüfung</span><span class="sxs-lookup"><span data-stu-id="eb826-130">Client certificate validation</span></span>

<span data-ttu-id="eb826-131">[Clientzertifikate](https://tools.ietf.org/html/rfc5246#section-7.4.4) werden zunächst überprüft, wenn die Verbindung hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="eb826-131">[Client certificates](https://tools.ietf.org/html/rfc5246#section-7.4.4) are initially validated when the connection is established.</span></span> <span data-ttu-id="eb826-132">Standardmäßig führt Kestrel keine zusätzliche Überprüfung des Clientzertifikats einer Verbindung durch.</span><span class="sxs-lookup"><span data-stu-id="eb826-132">By default, Kestrel doesn't perform additional validation of a connection's client certificate.</span></span>

<span data-ttu-id="eb826-133">Es wird empfohlen, dass gRPC-Dienste von Clientzertifikaten über das Paket [Microsoft.AspNetCore.Authentication.Certificate](xref:security/authentication/certauth) gesichert werden.</span><span class="sxs-lookup"><span data-stu-id="eb826-133">We recommend that gRPC services secured by client certificates use the [Microsoft.AspNetCore.Authentication.Certificate](xref:security/authentication/certauth) package.</span></span> <span data-ttu-id="eb826-134">Die Authentifizierung der ASP.NET Core-Zertifizierung führt zusätzliche Überprüfungen auf einem Clientzertifikat aus, einschließlich der folgenden:</span><span class="sxs-lookup"><span data-stu-id="eb826-134">ASP.NET Core certification authentication will perform additional validation on a client certificate, including:</span></span>

* <span data-ttu-id="eb826-135">Zertifikat verfügt über gültige erweiterte Schlüsselverwendung (EKU)</span><span class="sxs-lookup"><span data-stu-id="eb826-135">Certificate has a valid extended key use (EKU)</span></span>
* <span data-ttu-id="eb826-136">Befindet sich innerhalb des Gültigkeitszeitraums</span><span class="sxs-lookup"><span data-stu-id="eb826-136">Is within its validity period</span></span>
* <span data-ttu-id="eb826-137">Zertifikatswiderruf wird überprüft</span><span class="sxs-lookup"><span data-stu-id="eb826-137">Check certificate revocation</span></span>
