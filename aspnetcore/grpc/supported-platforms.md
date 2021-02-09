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
# <a name="grpc-on-net-supported-platforms"></a>gRPC auf .NET-unterstützten Plattformen

Von [James Newton-King](https://twitter.com/jamesnk)

In diesem Artikel werden die Anforderungen und unterstützten Plattformen für die Verwendung von gRPC mit .NET erläutert.

gRPC nutzt die in HTTP/2 verfügbaren erweiterten Features. HTTP/2 wird nicht überall unterstützt, aber ein zweites Übertragungsformat unter Verwendung von HTTP/1.1 ist für gRPC verfügbar:

* [`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md): gRPC über HTTP/2 ist die übliche Verwendung von gRPC.
* [`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md): gRPC-Web ändert das gRPC-Protokoll so, dass es mit HTTP/1.1 kompatibel ist. gRPC-Web kann an mehr Stellen verwendet werden, insbesondere kann es von Browser-Apps aufgerufen werden. Zwei erweiterte gRPC-Funktionen werden nicht mehr unterstützt: Clientstreaming und bidirektionales Streaming.

gRPC auf .NET unterstützt beide Übertragungsformate. gRPC über HTTP/2 wird standardmäßig verwendet. Informationen zum Einrichten von gRPC-Web finden Sie unter <xref:grpc/browser>.

## <a name="device-requirements"></a>Geräteanforderungen

gRPC auf .NET unterstützt alle Geräte, die von .NET Core unterstützt werden.

> [!div class="checklist"]
>
> * Windows
> * Linux
> * macOS&dagger;
> * Browser&Dagger;

&dagger;[macOS unterstützt das Hosting von ASP.NET Core-Apps mit HTTPS nicht](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos). gRPC-Clients auf macOS können Remotedienste aufrufen, die HTTPS verwenden.

&Dagger;Blazor WebAssembly: Apps können gRPC-Dienste mit gRPC-Web abrufen.

## <a name="aspnet-core-server-requirements"></a>ASP.NET Core-Serveranforderungen

gRPC-Dienste können auf allen integrierten ASP.NET Core-Servern gehostet werden.

> [!div class="checklist"]
>
> * Kestrel
> * TestServer
> * IIS&dagger;
> * HTTP.sys&Dagger;

&dagger;IIS erfordert .NET 5 und Windows 10 Build 20241 oder höher.

&Dagger;HTTP.sys erfordert .NET 5 und Windows 10 Build 19529 oder höher.

Informationen zum Konfigurieren von ASP.NET Core-Servern zum Ausführen von gRPC finden Sie unter <xref:grpc/aspnetcore#server-options>.

## <a name="net-version-requirements"></a>.NET-Versionsanforderungen

gRPC auf .NET unterstützt .NET Core 3 und .NET 5 oder höher.

> [!div class="checklist"]
>
> * .NET 5 oder höher
> * .NET Core 3

gRPC auf .NET unterstützt nicht die Ausführung auf .NET Framework und Xamarin. Die [gRPC-C#-Kernbibliothek](https://grpc.io/docs/languages/csharp/quickstart/) ist eine Drittanbieterbibliothek, die .NET Framework und Xamarin unterstützt. Der gRPC-C-Kern wird nicht von Microsoft unterstützt.

## <a name="azure-services"></a>Azure-Dienste

> [!div class="checklist"]
>
> * [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/)
> * [Azure App Service](https://azure.microsoft.com/services/app-service/)&dagger;

&dagger;Das Hosting von gRPC über HTTP/2 wird von Azure App Service nicht unterstützt. gRPC-Web ist eine kompatible Alternative.

Es wird an der Verbesserung der Unterstützung für gRPC mit HTTP/2 in Azure App Service gearbeitet. Weitere Informationen finden Sie in [diesem GitHub-Issue](https://github.com/dotnet/AspNetCore/issues/9020).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [gRPC-C#-Kernbibliothek](https://grpc.io/docs/languages/csharp/quickstart/)
