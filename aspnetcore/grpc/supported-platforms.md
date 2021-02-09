---
title: gRPC auf .NET-unterstützten Plattformen
author: jamesnk
description: Erfahren Sie mehr über die unterstützten Plattformen für gRPC auf .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/22/2021
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
uid: grpc/supported-platforms
ms.openlocfilehash: 6e48a19027f79b75edeebde9c584419871fba533
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530163"
---
# <a name="grpc-on-net-supported-platforms"></a><span data-ttu-id="c8c5b-103">gRPC auf .NET-unterstützten Plattformen</span><span class="sxs-lookup"><span data-stu-id="c8c5b-103">gRPC on .NET supported platforms</span></span>

<span data-ttu-id="c8c5b-104">Von [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="c8c5b-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="c8c5b-105">In diesem Artikel werden die Anforderungen und unterstützten Plattformen für die Verwendung von gRPC mit .NET erläutert.</span><span class="sxs-lookup"><span data-stu-id="c8c5b-105">This article discusses the requirements and supported platforms for using gRPC with .NET.</span></span>

<span data-ttu-id="c8c5b-106">gRPC nutzt die in HTTP/2 verfügbaren erweiterten Features.</span><span class="sxs-lookup"><span data-stu-id="c8c5b-106">gRPC takes advantage of advanced features available in  HTTP/2.</span></span> <span data-ttu-id="c8c5b-107">HTTP/2 wird nicht überall unterstützt, aber ein zweites Übertragungsformat unter Verwendung von HTTP/1.1 ist für gRPC verfügbar:</span><span class="sxs-lookup"><span data-stu-id="c8c5b-107">HTTP/2 isn't supported everywhere, but a second wire-format using HTTP/1.1 is available for gRPC:</span></span>

* <span data-ttu-id="c8c5b-108">[`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md): gRPC über HTTP/2 ist die übliche Verwendung von gRPC.</span><span class="sxs-lookup"><span data-stu-id="c8c5b-108">[`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) - gRPC over HTTP/2 is how gRPC is typically used.</span></span>
* <span data-ttu-id="c8c5b-109">[`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md): gRPC-Web ändert das gRPC-Protokoll so, dass es mit HTTP/1.1 kompatibel ist.</span><span class="sxs-lookup"><span data-stu-id="c8c5b-109">[`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) - gRPC-Web modifies the gRPC protocol to be compatible with HTTP/1.1.</span></span> <span data-ttu-id="c8c5b-110">gRPC-Web kann an mehr Stellen verwendet werden, insbesondere kann es von Browser-Apps aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="c8c5b-110">gRPC-Web can be used in more places, notably it is callable by browser apps.</span></span> <span data-ttu-id="c8c5b-111">Zwei erweiterte gRPC-Funktionen werden nicht mehr unterstützt: Clientstreaming und bidirektionales Streaming.</span><span class="sxs-lookup"><span data-stu-id="c8c5b-111">Two advanced gRPC features are no longer supported: client streaming and bidirectional streaming.</span></span>

<span data-ttu-id="c8c5b-112">gRPC auf .NET unterstützt beide Übertragungsformate.</span><span class="sxs-lookup"><span data-stu-id="c8c5b-112">gRPC on .NET supports both wire-formats.</span></span> <span data-ttu-id="c8c5b-113">gRPC über HTTP/2 wird standardmäßig verwendet.</span><span class="sxs-lookup"><span data-stu-id="c8c5b-113">gRPC over HTTP/2 is used by default.</span></span> <span data-ttu-id="c8c5b-114">Informationen zum Einrichten von gRPC-Web finden Sie unter <xref:grpc/browser>.</span><span class="sxs-lookup"><span data-stu-id="c8c5b-114">For information on setting up gRPC-Web, see <xref:grpc/browser>.</span></span>

## <a name="device-requirements"></a><span data-ttu-id="c8c5b-115">Geräteanforderungen</span><span class="sxs-lookup"><span data-stu-id="c8c5b-115">Device requirements</span></span>

<span data-ttu-id="c8c5b-116">gRPC auf .NET unterstützt alle Geräte, die von .NET Core unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="c8c5b-116">gRPC on .NET supports any device that .NET Core supports.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="c8c5b-117">Windows</span><span class="sxs-lookup"><span data-stu-id="c8c5b-117">Windows</span></span>
> * <span data-ttu-id="c8c5b-118">Linux</span><span class="sxs-lookup"><span data-stu-id="c8c5b-118">Linux</span></span>
> * <span data-ttu-id="c8c5b-119">macOS&dagger;</span><span class="sxs-lookup"><span data-stu-id="c8c5b-119">macOS&dagger;</span></span>
> * <span data-ttu-id="c8c5b-120">Browser&Dagger;</span><span class="sxs-lookup"><span data-stu-id="c8c5b-120">Browsers&Dagger;</span></span>

<span data-ttu-id="c8c5b-121">&dagger;[macOS unterstützt das Hosting von ASP.NET Core-Apps mit HTTPS nicht](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="c8c5b-121">&dagger;[macOS doesn't support hosting ASP.NET Core apps with HTTPS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span> <span data-ttu-id="c8c5b-122">gRPC-Clients auf macOS können Remotedienste aufrufen, die HTTPS verwenden.</span><span class="sxs-lookup"><span data-stu-id="c8c5b-122">gRPC clients on macOS can call remote services that use HTTPS.</span></span>

<span data-ttu-id="c8c5b-123">&Dagger;Blazor WebAssembly: Apps können gRPC-Dienste mit gRPC-Web abrufen.</span><span class="sxs-lookup"><span data-stu-id="c8c5b-123">&Dagger;Blazor WebAssembly apps can call gRPC services with gRPC-Web.</span></span>

## <a name="aspnet-core-server-requirements"></a><span data-ttu-id="c8c5b-124">ASP.NET Core-Serveranforderungen</span><span class="sxs-lookup"><span data-stu-id="c8c5b-124">ASP.NET Core server requirements</span></span>

<span data-ttu-id="c8c5b-125">gRPC-Dienste können auf allen integrierten ASP.NET Core-Servern gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="c8c5b-125">gRPC services can be hosted on all built-in ASP.NET Core servers.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="c8c5b-126">Kestrel</span><span class="sxs-lookup"><span data-stu-id="c8c5b-126">Kestrel</span></span>
> * <span data-ttu-id="c8c5b-127">TestServer</span><span class="sxs-lookup"><span data-stu-id="c8c5b-127">TestServer</span></span>
> * <span data-ttu-id="c8c5b-128">IIS&dagger;</span><span class="sxs-lookup"><span data-stu-id="c8c5b-128">IIS&dagger;</span></span>
> * <span data-ttu-id="c8c5b-129">HTTP.sys&Dagger;</span><span class="sxs-lookup"><span data-stu-id="c8c5b-129">HTTP.sys&Dagger;</span></span>

<span data-ttu-id="c8c5b-130">&dagger;IIS erfordert .NET 5 und Windows 10 Build 20241 oder höher.</span><span class="sxs-lookup"><span data-stu-id="c8c5b-130">&dagger;IIS requires .NET 5 and Windows 10 Build 20241 or later.</span></span>

<span data-ttu-id="c8c5b-131">&Dagger;HTTP.sys erfordert .NET 5 und Windows 10 Build 19529 oder höher.</span><span class="sxs-lookup"><span data-stu-id="c8c5b-131">&Dagger;HTTP.sys requires .NET 5 and Windows 10 Build 19529 or later.</span></span>

<span data-ttu-id="c8c5b-132">Informationen zum Konfigurieren von ASP.NET Core-Servern zum Ausführen von gRPC finden Sie unter <xref:grpc/aspnetcore#server-options>.</span><span class="sxs-lookup"><span data-stu-id="c8c5b-132">For information about configuring ASP.NET Core servers to run gRPC, see <xref:grpc/aspnetcore#server-options>.</span></span>

## <a name="net-version-requirements"></a><span data-ttu-id="c8c5b-133">.NET-Versionsanforderungen</span><span class="sxs-lookup"><span data-stu-id="c8c5b-133">.NET version requirements</span></span>

<span data-ttu-id="c8c5b-134">gRPC auf .NET unterstützt .NET Core 3 und .NET 5 oder höher.</span><span class="sxs-lookup"><span data-stu-id="c8c5b-134">gRPC on .NET supports .NET Core 3 and .NET 5 or later.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="c8c5b-135">.NET 5 oder höher</span><span class="sxs-lookup"><span data-stu-id="c8c5b-135">.NET 5 or later</span></span>
> * <span data-ttu-id="c8c5b-136">.NET Core 3</span><span class="sxs-lookup"><span data-stu-id="c8c5b-136">.NET Core 3</span></span>

<span data-ttu-id="c8c5b-137">gRPC auf .NET unterstützt nicht die Ausführung auf .NET Framework und Xamarin.</span><span class="sxs-lookup"><span data-stu-id="c8c5b-137">gRPC on .NET doesn't support running on .NET Framework and Xamarin.</span></span> <span data-ttu-id="c8c5b-138">Die [gRPC-C#-Kernbibliothek](https://grpc.io/docs/languages/csharp/quickstart/) ist eine Drittanbieterbibliothek, die .NET Framework und Xamarin unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c8c5b-138">[gRPC C# core-library](https://grpc.io/docs/languages/csharp/quickstart/) is a third party library that supports .NET Framework and Xamarin.</span></span> <span data-ttu-id="c8c5b-139">Der gRPC-C-Kern wird nicht von Microsoft unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c8c5b-139">gRPC C-core is not supported by Microsoft.</span></span>

## <a name="azure-services"></a><span data-ttu-id="c8c5b-140">Azure-Dienste</span><span class="sxs-lookup"><span data-stu-id="c8c5b-140">Azure services</span></span>

> [!div class="checklist"]
>
> * [<span data-ttu-id="c8c5b-141">Azure Kubernetes Service (AKS)</span><span class="sxs-lookup"><span data-stu-id="c8c5b-141">Azure Kubernetes Service (AKS)</span></span>](https://azure.microsoft.com/services/kubernetes-service/)
> * <span data-ttu-id="c8c5b-142">[Azure App Service](https://azure.microsoft.com/services/app-service/)&dagger;</span><span class="sxs-lookup"><span data-stu-id="c8c5b-142">[Azure App Service](https://azure.microsoft.com/services/app-service/)&dagger;</span></span>

<span data-ttu-id="c8c5b-143">&dagger;Das Hosting von gRPC über HTTP/2 wird von Azure App Service nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c8c5b-143">&dagger;Azure App Service doesn't support hosting gRPC over HTTP/2.</span></span> <span data-ttu-id="c8c5b-144">gRPC-Web ist eine kompatible Alternative.</span><span class="sxs-lookup"><span data-stu-id="c8c5b-144">gRPC-Web is a compatible alternative.</span></span>

<span data-ttu-id="c8c5b-145">Es wird an der Verbesserung der Unterstützung für gRPC mit HTTP/2 in Azure App Service gearbeitet.</span><span class="sxs-lookup"><span data-stu-id="c8c5b-145">Work is in-progress to improve support for gRPC with HTTP/2 in Azure App Service.</span></span> <span data-ttu-id="c8c5b-146">Weitere Informationen finden Sie in [diesem GitHub-Issue](https://github.com/dotnet/AspNetCore/issues/9020).</span><span class="sxs-lookup"><span data-stu-id="c8c5b-146">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/9020).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c8c5b-147">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="c8c5b-147">Additional resources</span></span>

* [<span data-ttu-id="c8c5b-148">gRPC-C#-Kernbibliothek</span><span class="sxs-lookup"><span data-stu-id="c8c5b-148">gRPC C# core-library</span></span>](https://grpc.io/docs/languages/csharp/quickstart/)
